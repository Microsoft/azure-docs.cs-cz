---
title: Nasazení webové aplikace s Azure Cosmos DB šablonou
description: Naučte se, jak nasadit účet Azure Cosmos DB, Azure App Service Web Apps a ukázkovou webovou aplikaci pomocí šablony Azure Resource Manager.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: sngun
ms.openlocfilehash: 2306dbe234e171ac613c33458df1990b767637df
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79128372"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Nasazení Azure Cosmos DB a Azure App Service Web Apps pomocí šablony Azure Resource Manager
V tomto kurzu se dozvíte, jak použít šablonu Azure Resource Manager k nasazení a integraci [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) webové aplikace a ukázkové webové aplikace.

Pomocí šablon Azure Resource Manager můžete snadno automatizovat nasazení a konfiguraci prostředků Azure.  V tomto kurzu se dozvíte, jak nasadit webovou aplikaci a automaticky nakonfigurovat Azure Cosmos DB informace o připojení účtu.

Po dokončení tohoto kurzu budete moci zodpovědět následující otázky:  

* Jak můžu použít šablonu Azure Resource Manager k nasazení a integraci Azure Cosmos DB účtu a webové aplikace v Azure App Service?
* Jak můžu použít šablonu Azure Resource Manager k nasazení a integraci účtu Azure Cosmos DB, webové aplikace v App Service Web Apps a aplikace WebDeploy?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Požadavky
> [!TIP]
> I když tento kurz nepředpokládá předchozí zkušenosti s Azure Resource Managermi šablonami nebo JSON, měli byste upravit odkazované šablony nebo možnosti nasazení, a proto se vyžaduje znalost každé z těchto oblastí.
> 
> 

Než budete postupovat podle pokynů v tomto kurzu, ujistěte se, že máte předplatné Azure. Azure je platforma založená na předplatném.  Další informace o získání předplatného najdete v tématu [možnosti nákupu](https://azure.microsoft.com/pricing/purchase-options/), [nabídky členů](https://azure.microsoft.com/pricing/member-offers/)nebo [bezplatné zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-download-the-template-files"></a><a id="CreateDB"></a>Krok 1: stažení souborů šablon
Pojďme začít stažením souborů šablon, které tento kurz vyžaduje.

1. Stáhněte si [účet vytvořit Azure Cosmos DB, Web Apps a nasaďte ukázkovou šablonu ukázkové aplikace](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) do místní složky (například C:\Azure Cosmos DBTemplates). Tato šablona nasadí účet Azure Cosmos DB, webovou aplikaci App Service a webovou aplikaci.  Také automaticky nakonfiguruje webovou aplikaci tak, aby se připojovala k účtu Azure Cosmos DB.
2. Stáhněte si šablonu [vytvořit účet Azure Cosmos DB a Web Apps ukázkové](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) šablony do místní složky (například C:\Azure Cosmos DBTemplates). Tato šablona nasadí účet Azure Cosmos DB, App Service webovou aplikaci, a upraví nastavení aplikace webu tak, aby bylo možné snadno obcházet informace Azure Cosmos DB připojení, ale nezahrnuje webovou aplikaci.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>Krok 2: nasazení Azure Cosmos DB účtu, App Service webové aplikace a ukázky ukázkové aplikace
Teď nasadíme vaši první šablonu.

> [!TIP]
> Šablona neověřuje, zda je název webové aplikace a název Azure Cosmos DB účtu zadaný v následující šabloně platný a b), které jsou k dispozici.  Důrazně doporučujeme ověřit dostupnost názvů, které plánujete před odesláním nasazení.
> 
> 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com), klikněte na nový a vyhledejte "Template Deployment".
    ![Snímek obrazovky uživatelského rozhraní nasazení šablony](./media/create-website/TemplateDeployment1.png)
2. Vyberte Template Deployment položku a klikněte na **vytvořit** ![snímek obrazovky uživatelského rozhraní nasazení šablony.](./media/create-website/TemplateDeployment2.png)
3. Klikněte na **Upravit šablonu**, vložte obsah souboru šablony DocDBWebsiteTodo. JSON a klikněte na **Uložit**.
   ![Snímek obrazovky uživatelského rozhraní nasazení šablony](./media/create-website/TemplateDeployment3.png)
4. Klikněte na **Upravit parametry**, zadejte hodnoty pro každý z povinných parametrů a klikněte na **OK**.  Funkce má následující parametry:
   
   1. NÁZEV_WEBU: Určuje App Service název webové aplikace a slouží k vytvoření adresy URL, kterou používáte pro přístup k webové aplikaci (například pokud zadáte "mydemodocdbwebapp", adresa URL, ke které přistupujete do webové aplikace, je `mydemodocdbwebapp.azurewebsites.net`).
   2. HOSTINGPLANNAME: Určuje název plánu hostování App Service, který se má vytvořit.
   3. Umístění: Určuje umístění Azure, ve kterém se mají vytvořit prostředky Azure Cosmos DB a webové aplikace.
   4. DATABASEACCOUNTNAME: Určuje název účtu Azure Cosmos DB, který se má vytvořit.   
      
      ![Snímek obrazovky uživatelského rozhraní nasazení šablony](./media/create-website/TemplateDeployment4.png)
5. Vyberte existující skupinu prostředků nebo zadejte název pro vytvoření nové skupiny prostředků a vyberte umístění pro skupinu prostředků.

    ![Snímek obrazovky uživatelského rozhraní nasazení šablony](./media/create-website/TemplateDeployment5.png)
6. Kliknutím na **zkontrolovat právní předpisy**, **koupit**a pak kliknutím na **vytvořit** zahajte nasazení.  Vyberte **Připnout na řídicí panel** , aby se výsledné nasazení bylo možné snadno zobrazit na domovské stránce Azure Portal.
   ![Snímek obrazovky uživatelského rozhraní nasazení šablony](./media/create-website/TemplateDeployment6.png)
7. Po dokončení nasazení se otevře podokno skupina prostředků.
   ![Snímek obrazovky s podoknem skupiny prostředků](./media/create-website/TemplateDeployment7.png)  
8. Chcete-li použít aplikaci, přejděte na adresu URL webové aplikace (v příkladu výše bude adresa URL `http://mydemodocdbwebapp.azurewebsites.net`).  Zobrazí se následující webová aplikace:
   
   ![Ukázková aplikace todo](./media/create-website/image2.png)
9. Pokračujte a vytvořte ve webové aplikaci několik úkolů a pak se vraťte do podokna Skupina prostředků v Azure Portal. V seznamu prostředky klikněte na prostředek účtu Azure Cosmos DB a pak klikněte na **Průzkumník dat**.
10. Spusťte výchozí dotaz "vybrat * z c" a zkontrolujte výsledky.  Všimněte si, že dotaz načetl vyjádření položek todo, které jste vytvořili v kroku 7 výše.  Nebojte se s dotazy a experimentovat. například zkuste spustit příkaz SELECT * FROM c, kde c. Ko Complete = true vrátí všechny položky ToDo, které byly označeny jako dokončené.
11. Využijte možnost prozkoumat prostředí portálu Azure Cosmos DB nebo upravte ukázkovou aplikaci todo.  Až budete připraveni, nasadíme další šablonu.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Krok 3: nasazení účtu dokumentu a ukázky webové aplikace
Teď nasadíme druhou šablonu.  Tato šablona je užitečná k tomu, abyste viděli, jak můžete vložit informace o Azure Cosmos DB připojení, jako je koncový bod účtu a hlavní klíč, do webové aplikace jako nastavení aplikace nebo jako vlastní připojovací řetězec. Například můžete mít vlastní webovou aplikaci, kterou byste chtěli nasadit s účtem Azure Cosmos DB a mít informace o připojení automaticky vyplněné během nasazování.

> [!TIP]
> Šablona neověřuje, zda je název webové aplikace a Azure Cosmos DB název účtu, který je uveden níže, platný a b), který je k dispozici.  Důrazně doporučujeme ověřit dostupnost názvů, které plánujete před odesláním nasazení.
> 
> 

1. Na webu [Azure Portal](https://portal.azure.com)klikněte na nový a vyhledejte "Template Deployment".
    ![Snímek obrazovky uživatelského rozhraní nasazení šablony](./media/create-website/TemplateDeployment1.png)
2. Vyberte Template Deployment položku a klikněte na **vytvořit** ![snímek obrazovky uživatelského rozhraní nasazení šablony.](./media/create-website/TemplateDeployment2.png)
3. Klikněte na **Upravit šablonu**, vložte obsah souboru šablony DocDBWebSite. JSON a klikněte na **Uložit**.
   ![Snímek obrazovky uživatelského rozhraní nasazení šablony](./media/create-website/TemplateDeployment3.png)
4. Klikněte na **Upravit parametry**, zadejte hodnoty pro každý z povinných parametrů a klikněte na **OK**.  Funkce má následující parametry:
   
   1. NÁZEV_WEBU: Určuje App Service název webové aplikace a slouží k vytvoření adresy URL, kterou budete používat pro přístup k webové aplikaci (Pokud například zadáte "mydemodocdbwebapp", pak adresa URL, na kterou přistupujete k webové aplikaci, je mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: Určuje název plánu hostování App Service, který se má vytvořit.
   3. Umístění: Určuje umístění Azure, ve kterém se mají vytvořit prostředky Azure Cosmos DB a webové aplikace.
   4. DATABASEACCOUNTNAME: Určuje název účtu Azure Cosmos DB, který se má vytvořit.   
      
      ![Snímek obrazovky uživatelského rozhraní nasazení šablony](./media/create-website/TemplateDeployment4.png)
5. Vyberte existující skupinu prostředků nebo zadejte název pro vytvoření nové skupiny prostředků a vyberte umístění pro skupinu prostředků.

    ![Snímek obrazovky uživatelského rozhraní nasazení šablony](./media/create-website/TemplateDeployment5.png)
6. Kliknutím na **zkontrolovat právní předpisy**, **koupit**a pak kliknutím na **vytvořit** zahajte nasazení.  Vyberte **Připnout na řídicí panel** , aby se výsledné nasazení bylo možné snadno zobrazit na domovské stránce Azure Portal.
   ![Snímek obrazovky uživatelského rozhraní nasazení šablony](./media/create-website/TemplateDeployment6.png)
7. Po dokončení nasazení se otevře podokno skupina prostředků.
   ![Snímek obrazovky s podoknem skupiny prostředků](./media/create-website/TemplateDeployment7.png)  
8. Klikněte na prostředek webové aplikace v seznamu prostředky a potom klikněte na snímek obrazovky **Nastavení** ![aplikace pro skupinu prostředků.](./media/create-website/TemplateDeployment9.png)  
9. Všimněte si, jak jsou k dispozici nastavení aplikace pro koncový bod Azure Cosmos DB a všechny hlavní klíče Azure Cosmos DB.

    ![Snímek obrazovky s nastavením aplikace](./media/create-website/TemplateDeployment10.png)  
10. Nebojte se, že budete pokračovat v zkoumání webu Azure Portal, nebo pomocí jednoho z našich Azure Cosmos DB [ukázek](https://go.microsoft.com/fwlink/?LinkID=402386) vytvořit vlastní Azure Cosmos DBovou aplikaci.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Další kroky
Blahopřejeme! Nasadili jste Azure Cosmos DB, App Service webové aplikace a ukázkovou webovou aplikaci pomocí šablon Azure Resource Manager.

* Pokud se chcete dozvědět víc o Azure Cosmos DB, klikněte [sem](https://azure.microsoft.com/services/cosmos-db/).
* Další informace o Azure App Service Web Apps získáte kliknutím [sem](https://go.microsoft.com/fwlink/?LinkId=325362).
* Další informace o šablonách Azure Resource Manager získáte kliknutím [sem](https://msdn.microsoft.com/library/azure/dn790549.aspx).

## <a name="whats-changed"></a>Co se změnilo
* Průvodce změnou z webů na App Service najdete v tématu [Azure App Service a jeho dopad na stávající služby Azure](https://go.microsoft.com/fwlink/?LinkId=529714) .

> [!NOTE]
> Pokud chcete začít používat Azure App Service před registrací účtu Azure, přejděte k [možnosti vyzkoušet si App Service](https://go.microsoft.com/fwlink/?LinkId=523751), kde si můžete hned vytvořit krátkodobou úvodní webovou aplikaci. Nevyžaduje se žádná platební karta a nevzniká žádný závazek.
> 
> 

