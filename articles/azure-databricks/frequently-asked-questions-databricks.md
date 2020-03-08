---
title: 'Azure Databricks: běžné dotazy a pomáhat'
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
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671564"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Nejčastější dotazy týkající se Azure Databricks

V tomto článku jsou uvedené Nejčastější dotazy, které se vám mohly vzjímat s Azure Databricks. Obsahuje taky některé běžné problémy, které jste mohli mít při používání datacihlů. Další informace najdete v tématu [co je Azure Databricks](what-is-azure-databricks.md). 

## <a name="can-i-use-azure-key-vault-to-store-keyssecrets-to-be-used-in-azure-databricks"></a>Můžu použít Azure Key Vault k ukládání klíčů/tajných klíčů, které se mají použít v Azure Databricks?
Ano. Pomocí Azure Key Vault můžete ukládat klíče a tajné kódy pro použití s Azure Databricks. Další informace najdete v tématu [Azure Key Vault obory](/azure/databricks/security/secrets/secret-scopes).


## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Můžu používat virtuální sítě Azure s datacihly?
Ano. Můžete použít Azure Virtual Network (VNET) s Azure Databricks. Další informace najdete v tématu [nasazení Azure Databricks ve službě Azure Virtual Network](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

## <a name="how-do-i-access-azure-data-lake-storage-from-a-notebook"></a>Návody přistupovat Azure Data Lake Storage z poznámkového bloku? 

Postupujte následovně:
1. Ve službě Azure Active Directory (Azure AD) zřiďte instanční objekt a zaznamenejte jeho klíč.
1. Přiřaďte potřebná oprávnění k instančnímu objektu v Data Lake Storage.
1. Pokud chcete získat přístup k souboru v Data Lake Storage, použijte přihlašovací údaje instančního objektu ve službě Poznámkový blok.

Další informace najdete v tématu [použití Azure Data Lake Storage s Azure Databricks](/azure/databricks/data/data-sources/azure/azure-datalake).

## <a name="fix-common-problems"></a>Řešení běžných problémů

Tady je několik problémů, se kterými se můžete setkat s datacihly.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Problém: v tomto předplatném není zaregistrované používání oboru názvů Microsoft. datacihly.

#### <a name="error-message"></a>Chybová zpráva

"Toto předplatné není zaregistrované pro používání oboru názvů" Microsoft. datacihly ". Postup registrace předplatných najdete v tématu https://aka.ms/rps-not-found. (Kód: MissingSubscriptionRegistration) "

#### <a name="solution"></a>Řešení

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Vyberte **předplatná**, předplatné, které používáte, a pak **poskytovatele prostředků**. 
1. V seznamu poskytovatelů prostředků s **cihlami Microsoft. datacihly**vyberte **Registrovat**. Abyste mohli registrovat poskytovatele prostředků, musíte mít v předplatném roli přispěvatel nebo vlastník.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Problém: váš účet {email} nemá roli vlastníka nebo přispěvatele v prostředku pracovního prostoru datacihly v Azure Portal

#### <a name="error-message"></a>Chybová zpráva

"Váš účet {email} nemá roli vlastníka nebo přispěvatele v prostředku pracovního prostoru datacihly v Azure Portal. K této chybě může dojít také v případě, že jste uživatel typu Host v tenantovi. Požádejte správce, aby vám udělil přístup, nebo ho přidejte jako uživatele přímo do pracovního prostoru datacihly. 

#### <a name="solution"></a>Řešení

Toto je několik řešení tohoto problému:

* Chcete-li inicializovat klienta, musíte být přihlášeni jako běžný uživatel tenanta, nikoli jako uživatel typu Host. Musíte mít také roli Přispěvatel v prostředku pracovního prostoru datacihly. Přístup uživatele můžete udělit z karty **řízení přístupu (IAM)** v pracovním prostoru datacihly v Azure Portal.

* K této chybě může dojít také v případě, že je název domény e-mailu přiřazen více adresářům ve službě Azure AD. Pokud chcete tento problém obejít, vytvořte nového uživatele v adresáři, který obsahuje předplatné s vaším pracovním prostorem datacihly.

    a. V Azure Portal přejdete do služby Azure AD. Vyberte **Uživatelé a skupiny** > **Přidat uživatele**.

    b. Místo `@<your_domain>` e-mailu přidejte uživatele s `@<tenant_name>.onmicrosoft.com` e-mailem. Tuto možnost můžete najít ve **vlastních doménách**v části Azure AD v Azure Portal.
    
    c. Tomuto novému uživateli udělte roli **Přispěvatel** v prostředku pracovního prostoru datacihly.
    
    d. Přihlaste se k Azure Portal pomocí nového uživatele a najděte pracovní prostor datacihly.
    
    e. Jako tohoto uživatele spusťte pracovní prostor datacihly.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Problém: váš účet {email} není zaregistrovaný v datacihlech. 

#### <a name="solution"></a>Řešení

Pokud jste pracovní prostor nevytvořili a Přidali jste ho jako uživatel, obraťte se na osobu, která pracovní prostor vytvořila. Přidejte tuto osobu pomocí konzoly pro správu Azure Databricks. Pokyny najdete v tématu [Přidání a Správa uživatelů](/azure/databricks/administration-guide/users-groups/users). Pokud jste vytvořili pracovní prostor a stále se zobrazí tato chyba, zkuste z Azure Portal vybrat možnost **inicializovat pracovní prostor** znovu.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Problém: poskytovatel cloudu selhal při nastavování clusteru (PublicIPCountLimitReached).

#### <a name="error-message"></a>Chybová zpráva

"Nepovedlo se spustit poskytovatele cloudu: při nastavování clusteru došlo k chybě poskytovatele cloudu. Další informace najdete v příručce pro datacihly. Kód chyby Azure: PublicIPCountLimitReached. Chybová zpráva Azure: pro toto předplatné v této oblasti nejde vytvořit více než 10 veřejných IP adres.

#### <a name="background"></a>Pozadí

Clustery datacihly používají jednu veřejnou IP adresu na uzel (včetně uzlu ovladače). Předplatná Azure mají [omezení veřejných IP adres](/azure/azure-resource-manager/management/azure-subscription-service-limits#publicip-address) na oblast. Vytvoření clusteru a operace škálování můžou selhat, pokud by počet veřejných IP adres přidělených k tomuto předplatnému v této oblasti překročil limit. Tento limit zahrnuje také veřejné IP adresy přidělené pro použití bez datacihlů, jako jsou například vlastní uživatelem definované virtuální počítače.

Obecně platí, že clustery využívají jenom veřejné IP adresy, když jsou aktivní. `PublicIPCountLimitReached` chyby ale můžou pořád nastat po krátké době i po ukončení ostatních clusterů. Důvodem je to, že datacihly dočasně uloží prostředky Azure do mezipaměti, když se cluster ukončí. Ukládání prostředků do mezipaměti je záměrné, protože výrazně snižuje latenci spouštění a automatického škálování clusteru v mnoha běžných scénářích.

#### <a name="solution"></a>Řešení

Pokud vaše předplatné již dosáhlo svého limitu veřejných IP adres pro danou oblast, měli byste provést jednu nebo druhou z následujících akcí.

- Vytvořte nové clustery v jiném pracovním prostoru datacihly. Druhý pracovní prostor se musí nacházet v oblasti, ve které jste nedosáhli limitu veřejných IP adres vašeho předplatného.
- [Požadavek na zvýšení limitu vaší veřejné IP adresy](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request). Jako **typ problému**vyberte **kvóta** a v části **síť: ARM** jako **typ kvóty**. V **podrobnostech**si vyžádejte zvýšení kvóty veřejných IP adres. Například pokud je váš limit aktuálně 60 a chcete vytvořit cluster 100, požádejte o zvýšení limitu na 160.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Problém: druhý typ neúspěšného spuštění poskytovatele cloudu při nastavování clusteru (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Chybová zpráva

"Nepovedlo se spustit poskytovatele cloudu: při nastavování clusteru došlo k chybě poskytovatele cloudu. Další informace najdete v příručce pro datacihly.
Kód chyby Azure: MissingSubscriptionRegistration chybová zpráva Azure: předplatné není zaregistrované pro používání oboru názvů Microsoft. Compute. Postup registrace předplatných najdete v tématu https://aka.ms/rps-not-found.

#### <a name="solution"></a>Řešení

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Vyberte **předplatná**, předplatné, které používáte, a pak **poskytovatele prostředků**. 
1. V seznamu poskytovatelů prostředků vyberte v části **Microsoft. COMPUTE**možnost **Registrovat**. Abyste mohli registrovat poskytovatele prostředků, musíte mít v předplatném roli přispěvatel nebo vlastník.

Podrobnější pokyny najdete v tématu [poskytovatelé a typy prostředků](../azure-resource-manager/management/resource-providers-and-types.md).

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Problém: Azure Databricks potřebuje oprávnění pro přístup k prostředkům ve vaší organizaci, který může udělit jenom správce.

#### <a name="background"></a>Pozadí

Azure Databricks je integrována s Azure Active Directory. Můžete nastavit oprávnění v rámci Azure Databricks (například v poznámkových blocích nebo clusterech) zadáním uživatelů ze služby Azure AD. Aby bylo možné Azure Databricks Zobrazit jména uživatelů ze služby Azure AD, vyžaduje, aby tyto informace a souhlas bylo uděleno oprávněním ke čtení. Pokud souhlas ještě není k dispozici, zobrazí se chyba.

#### <a name="solution"></a>Řešení

Přihlaste se jako globální správce Azure Portal. V případě Azure Active Directory přejít na kartu **uživatelská nastavení** a ujistěte se, že **Uživatelé můžou udělit souhlas s aplikacemi, které přistupují k firemním datům** , je nastaveno na **Ano**.

## <a name="next-steps"></a>Další kroky

- [Rychlý Start: Začínáme s Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [Co je Azure Databricks?](what-is-azure-databricks.md)
