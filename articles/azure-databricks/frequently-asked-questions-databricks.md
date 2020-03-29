---
title: 'Azure Databricks: Běžné otázky a pomoc'
description: Získejte odpovědi na běžné otázky a informace o řešení potíží o Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 8d7aab43641c6c594ff60368ccb3810e0c060dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671564"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Nejčastější dotazy týkající se Azure Databricks

V tomto článku jsou uvedeny hlavní otázky, které by mohly souviset s Azure Databricks. Obsahuje také seznam některých běžných problémů, které můžete mít při používání databricks. Další informace najdete v tématu [Co je Azure Databricks](what-is-azure-databricks.md). 

## <a name="can-i-use-azure-key-vault-to-store-keyssecrets-to-be-used-in-azure-databricks"></a>Můžu použít Azure Key Vault k ukládání klíčů nebo tajných klíčů, které se mají používat v Azure Databricks?
Ano. Azure Key Vault můžete použít k ukládání klíčů nebo tajných klíčů pro použití s Azure Databricks. Další informace naleznete v [tématu Azure Key Vault-couval obory](/azure/databricks/security/secrets/secret-scopes).


## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Můžu virtuální sítě Azure používat s datovými cihlami?
Ano. Virtuální síť Azure (VNET) můžete použít s Azure Databricks. Další informace najdete [v tématu Nasazení Datových cihel Azure ve virtuální síti Azure](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

## <a name="how-do-i-access-azure-data-lake-storage-from-a-notebook"></a>Jak se dostanu k Azure Data Lake Storage z poznámkového bloku? 

Postupujte následovně:
1. Ve službě Azure Active Directory (Azure AD) zřídíte instanční objekt a zaznamenejte jeho klíč.
1. Přiřaďte potřebná oprávnění instančnímu objektu v úložišti datového jezera.
1. Chcete-li získat přístup k souboru v úložišti datového jezera, použijte pověření hlavního povinného zápisníku v poznámkovém bloku.

Další informace najdete [v tématu Použití Azure Data Lake Storage s Azure Databricks](/azure/databricks/data/data-sources/azure/azure-datalake).

## <a name="fix-common-problems"></a>Oprava běžných problémů

Zde je několik problémů, se kterými se můžete setkat s Databricks.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Problém: Toto předplatné není registrováno pro použití oboru názvů Microsoft.Databricks

#### <a name="error-message"></a>Chybová zpráva

"Toto předplatné není registrováno pro použití oboru názvů Microsoft.Databricks. Podívejte https://aka.ms/rps-not-found se, jak zaregistrovat odběry. (Kód: MissingSubscriptionRegistration)"

#### <a name="solution"></a>Řešení

1. Přejděte na [portál Azure](https://portal.azure.com).
1. Vyberte **Předplatná**, předplatné, které používáte, a potom **vyberte zprostředkovatele prostředků**. 
1. V seznamu poskytovatelů prostředků ve skutečnosti **microsoft.databricks**vyberte **Registrovat**. Chcete-li zaregistrovat poskytovatele prostředků, musíte mít v předplatném roli přispěvatele nebo vlastníka.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Problém: Váš účet {e-mail} nemá roli vlastníka nebo přispěvatele v prostředku pracovního prostoru Databricks na webu Azure Portal.

#### <a name="error-message"></a>Chybová zpráva

"Váš účet {email} nemá roli vlastníka nebo přispěvatele na prostředku pracovního prostoru Databricks na webu Azure Portal. K této chybě může dojít také v případě, že jste uživatelem typu Host v tenantovi. Požádejte správce, aby vám udělil přístup nebo vás přidal jako uživatele přímo v pracovním prostoru Databricks." 

#### <a name="solution"></a>Řešení

Následuje několik řešení tohoto problému:

* Chcete-li inicializovat klienta, musíte být přihlášeni jako běžný uživatel klienta, nikoli jako uživatel typu Host. Musíte mít také roli přispěvatele na prostředek pracovního prostoru Databricks. Uživateli můžete udělit přístup z karty **Řízení přístupu (IAM)** v pracovním prostoru Databricks na webu Azure Portal.

* K této chybě může dojít také v případě, že název e-mailové domény je přiřazen k více adresářů ve službě Azure AD. Chcete-li tento problém vyřešit, vytvořte nového uživatele v adresáři, který obsahuje předplatné s pracovním prostorem Databricks.

    a. Na webu Azure Portal přejděte na Azure AD. Vyberte **Uživatelé a skupiny** > **Přidat uživatele**.

    b. Přidejte uživatele `@<tenant_name>.onmicrosoft.com` pomocí e-mailu místo `@<your_domain>` e-mailu. Tuto možnost najdete ve **vlastních doménách**v části Azure AD na webu Azure Portal.
    
    c. Udělte tomuto novému uživateli roli **přispěvatele** v prostředku pracovního prostoru Databricks.
    
    d. Přihlaste se k portálu Azure s novým uživatelem a najděte pracovní prostor Databricks.
    
    e. Spusťte pracovní prostor Databricks jako tento uživatel.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Problém: Váš účet {email} nebyl zaregistrován v Databricks. 

#### <a name="solution"></a>Řešení

Pokud jste pracovní prostor nevytvořili a jste přidáni jako uživatel, obraťte se na osobu, která pracovní prostor vytvořila. Nech te, aby vás tato osoba přidala pomocí Konzoly pro správu Azure Databricks. Pokyny naleznete v tématu [Přidání a správa uživatelů](/azure/databricks/administration-guide/users-groups/users). Pokud jste vytvořili pracovní prostor a stále se zobrazí tato chyba, zkuste znovu vybrat **Inicializovat pracovní prostor** z webu Azure Portal.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Problém: Selhání spuštění zprostředkovatele cloudu při nastavování clusteru (PublicIPCountLimitReached)

#### <a name="error-message"></a>Chybová zpráva

"Selhání spuštění poskytovatele cloudu: Při nastavování clusteru došlo k chybě zprostředkovatele cloudu. Další informace naleznete v průvodci Databricks. Kód chyby Azure: PublicIPCountLimitReached. Chybová zpráva Azure: Pro toto předplatné v této oblasti nelze vytvořit více než 10 veřejných IP adres."

#### <a name="background"></a>Pozadí

Clustery Databricks používají jednu veřejnou IP adresu na uzel (včetně uzlu ovladače). Předplatná Azure mají [omezení veřejných IP adres](/azure/azure-resource-manager/management/azure-subscription-service-limits#publicip-address) na oblast. Proto operace vytváření clusteru a škálování může selhat, pokud by způsobily, že počet veřejných IP adres přidělených tomuto předplatnému v této oblasti překročí limit. Toto omezení zahrnuje také veřejné IP adresy přidělené pro použití mimo Databricks, jako jsou vlastní uživatelem definované virtuální počítače.

Clustery obecně spotřebovávají pouze veřejné IP adresy, když jsou aktivní. Chyby `PublicIPCountLimitReached` však může nadále dochází po krátkou dobu i po ukončení jiných clusterů. Důvodem je, že Databricks dočasně ukládá prostředky Azure při ukončení clusteru. Ukládání prostředků do mezipaměti je záměrné, protože výrazně snižuje latenci spuštění clusteru a automatické škálování v mnoha běžných scénářích.

#### <a name="solution"></a>Řešení

Pokud vaše předplatné již dosáhlo limitu veřejné IP adresy pro danou oblast, měli byste udělat jednu nebo druhou z následujících možností.

- Vytvořte nové clustery v jiném pracovním prostoru Databricks. Druhý pracovní prostor musí být umístěn v oblasti, ve které jste nedosáhli limitu veřejné IP adresy vašeho předplatného.
- [Žádost o zvýšení limitu veřejné IP adresy](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request). Zvolte **Kvóta** jako **typ problému**a **Síť: ARM** jako typ **kvóty**. V **části Podrobnosti**požádejte o zvýšení kvóty veřejné IP adresy. Například pokud je váš limit aktuálně 60 a chcete vytvořit cluster se 100 uzlem, požádejte o zvýšení limitu na 160.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Problém: Druhý typ selhání spuštění poskytovatele cloudu při nastavování clusteru (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Chybová zpráva

"Selhání spuštění poskytovatele cloudu: Při nastavování clusteru došlo k chybě zprostředkovatele cloudu. Další informace naleznete v průvodci Databricks.
Kód chyby Azure: MissingSubscriptionRegistration Azure chybová zpráva: Odběr není registrován pro použití oboru názvů 'Microsoft.Compute'. Přečtěte si, https://aka.ms/rps-not-found jak zaregistrovat odběry."

#### <a name="solution"></a>Řešení

1. Přejděte na [portál Azure](https://portal.azure.com).
1. Vyberte **Předplatná**, předplatné, které používáte, a potom **vyberte zprostředkovatele prostředků**. 
1. V seznamu poskytovatelů prostředků ve skutečnosti **microsoft.compute**vyberte **Registrovat**. Chcete-li zaregistrovat poskytovatele prostředků, musíte mít v předplatném roli přispěvatele nebo vlastníka.

Podrobnější pokyny naleznete v tématu [Zprostředkovatelé a typy prostředků](../azure-resource-manager/management/resource-providers-and-types.md).

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Problém: Azure Databricks potřebuje oprávnění pro přístup k prostředkům ve vaší organizaci, které může udělit jenom správce.

#### <a name="background"></a>Pozadí

Azure Databricks je integrovaný s Azure Active Directory. Oprávnění v rámci Azure Databricks (například u poznámkových bloků nebo clusterů) můžete nastavit zadáním uživatelů z Azure AD. Aby Azure Databricks mohli uvést jména uživatelů z vašeho Azure AD, vyžaduje oprávnění ke čtení těchto informací a souhlas, který má být udělen. Pokud souhlas ještě není k dispozici, zobrazí se chyba.

#### <a name="solution"></a>Řešení

Přihlaste se jako globální správce portálu Azure. Ve službě Azure Active Directory přejděte na kartu **Uživatelská nastavení** a ujistěte se, že **uživatelé mohou souhlasit s tím, aby aplikace přistupující k firemním datům jejich jménem** byly nastaveny na **Ano**.

## <a name="next-steps"></a>Další kroky

- [Úvodní příručka: Začínáme s Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [Co je Azure Databricks?](what-is-azure-databricks.md)
