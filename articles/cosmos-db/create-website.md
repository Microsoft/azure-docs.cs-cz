---
title: Nasazení webové aplikace pomocí šablony – Azure Cosmos DB
description: Informace o nasazení účtu služby Azure Cosmos DB, Azure App Service Web Apps a ukázkovou webovou aplikaci pomocí šablony Azure Resource Manageru.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: sngun
ms.openlocfilehash: 2306dbe234e171ac613c33458df1990b767637df
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128372"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Nasazení služby Azure Cosmos DB a Azure App Service Web Apps pomocí šablony Azure Resource Manageru
V tomto kurzu se dozvíte, jak použít šablonu Azure Resource Manager k nasazení a integraci [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) webové aplikace a ukázkové webové aplikace.

Pomocí šablon Azure Resource Manageru, můžete snadno automatizovat nasazení a konfiguraci vašich prostředků Azure.  Tento kurz ukazuje, jak nasadit webovou aplikaci a automaticky konfigurovat informace o připojení účtu služby Azure Cosmos DB.

Po dokončení tohoto kurzu, budete moci odpovědět na následující otázky:  

* Jak lze pomocí šablony Azure Resource Manageru k nasazení a integrace účtu služby Azure Cosmos DB a webovou aplikaci ve službě Azure App Service?
* Jak lze pomocí šablony Azure Resource Manageru k nasazení a integrace účtu služby Azure Cosmos DB, webové aplikace v App Service Web Apps a aplikaci Webdeploy?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Předpoklady
> [!TIP]
> Při tomto kurzu nepředpokládá zkušenosti s pomocí šablon Azure Resource Manageru nebo formátu JSON, by měl, kterou chcete upravit odkazované šablony nebo možnosti nasazení, informace o každé z těchto oblastí je nutné.
> 
> 

Než budete postupovat podle pokynů v tomto kurzu, ujistěte se, že máte předplatné Azure. Azure je platforma založená na předplatném.  Další informace o získání předplatného najdete v tématu [možnosti nákupu](https://azure.microsoft.com/pricing/purchase-options/), [nabídky členů](https://azure.microsoft.com/pricing/member-offers/)nebo [bezplatné zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a id="CreateDB"></a>Krok 1: stažení souborů šablon
Začněme tím, že si stáhnete soubory šablony, které tento kurz vyžaduje.

1. Stáhněte si [účet vytvořit Azure Cosmos DB, Web Apps a nasaďte ukázkovou šablonu ukázkové aplikace](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) do místní složky (například C:\Azure Cosmos DBTemplates). Tato šablona nasadí účet služby Azure Cosmos DB, webová aplikace služby App Service a webovou aplikaci.  Také automaticky nakonfiguruje webové aplikace pro připojení k účtu Azure Cosmos DB.
2. Stáhněte si šablonu [vytvořit účet Azure Cosmos DB a Web Apps ukázkové](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) šablony do místní složky (například C:\Azure Cosmos DBTemplates). Tato šablona nasadí účet služby Azure Cosmos DB, webovou aplikaci služby App Service a změní nastavení lokality aplikace lze snadno surface informace o připojení služby Azure Cosmos DB, ale nezahrnuje webové aplikace.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>Krok 2: Nasazení účtu Azure Cosmos DB, webová aplikace App Service a demonstrační Ukázka aplikace
Teď nasadíme první šablony.

> [!TIP]
> Šablonu nelze ověřit, že jsou název webové aplikace a zadané v šabloně následující název účtu služby Azure Cosmos DB a) platná a (b) k dispozici.  Důrazně doporučujeme, abyste ověřili dostupnost názvy, které chcete zadat před odesláním nasazení.
> 
> 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com), klikněte na nový a vyhledejte "Template Deployment".
    ![snímku uživatelského rozhraní nasazení šablony](./media/create-website/TemplateDeployment1.png)
2. Vyberte Template deployment položku a klikněte na tlačítko **vytvořit** ![obrazovky uživatelského rozhraní nasazení šablony](./media/create-website/TemplateDeployment2.png)
3. Klikněte na **Upravit šablonu**, vložte obsah souboru šablony DocDBWebsiteTodo. JSON a klikněte na **Uložit**.
   ![snímku uživatelského rozhraní nasazení šablony](./media/create-website/TemplateDeployment3.png)
4. Klikněte na **Upravit parametry**, zadejte hodnoty pro každý z povinných parametrů a klikněte na **OK**.  Parametry jsou následující:
   
   1. NÁZEV_WEBU: Určuje název webové aplikace App Service a slouží k vytvoření adresy URL, kterou používáte pro přístup k webové aplikaci (například pokud zadáte "mydemodocdbwebapp", adresa URL, na kterou se přistupujete k webové aplikaci, `mydemodocdbwebapp.azurewebsites.net`).
   2. HOSTINGPLANNAME: Určuje název plánu hostování služby App Service vytvořit.
   3. UMÍSTĚNÍ: Určuje umístění Azure, ve kterém chcete vytvořit službu Azure Cosmos DB a webových prostředků aplikace.
   4. DATABASEACCOUNTNAME: Určuje název účtu služby Azure Cosmos DB k vytvoření.   
      
      ![Snímek obrazovky uživatelské rozhraní pro nasazení šablony](./media/create-website/TemplateDeployment4.png)
5. Zvolte existující skupinu prostředků nebo zadejte název, který chcete vytvořit novou skupinu prostředků a vyberte umístění pro skupinu prostředků.

    ![Snímek obrazovky uživatelské rozhraní pro nasazení šablony](./media/create-website/TemplateDeployment5.png)
6. Kliknutím na **zkontrolovat právní předpisy**, **koupit**a pak kliknutím na **vytvořit** zahajte nasazení.  Vyberte **Připnout na řídicí panel** , aby se výsledné nasazení bylo možné snadno zobrazit na domovské stránce Azure Portal.
   ![snímku uživatelského rozhraní nasazení šablony](./media/create-website/TemplateDeployment6.png)
7. Až se nasazení dokončí, otevře se podokno skupiny prostředků.
   ![snímku podokna skupiny prostředků](./media/create-website/TemplateDeployment7.png)  
8. Pokud chcete použít aplikaci, přejděte na adresu URL webové aplikace (v příkladu výše `http://mydemodocdbwebapp.azurewebsites.net`adresa URL).  Zobrazí se vám následující webové aplikaci:
   
   ![Ukázková aplikace seznamu úkolů](./media/create-website/image2.png)
9. Pokračujte a vytvořte několik úloh ve webové aplikaci a pak se vraťte do podokna skupiny prostředků na webu Azure Portal. V seznamu prostředky klikněte na prostředek účtu Azure Cosmos DB a pak klikněte na **Průzkumník dat**.
10. Spusťte dotaz výchozí "vybrat * FROM c" a zkontrolovat výsledky.  Všimněte si, že dotaz načte reprezentaci JSON, který jste vytvořili v kroku 7 výše uvedených položek todo.  Bez obav experimentovat s dotazy; například, zkuste spustit vyberte * FROM c WHERE c.isComplete = true, pokud chcete vrátit všechny položky seznamu úkolů, které jsou označené jako dokončené.
11. Můžete prozkoumat práce s portálem Azure Cosmos DB nebo upravit ukázkovou aplikaci seznamu úkolů.  Jakmile budete připraveni, nasadíme jiné šablony.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Krok 3: Nasazení ukázky dokumentu účtu a webové aplikace
Teď nasadíme Druhá šablona.  Tato šablona je vhodné zobrazit, jak můžete vložit informace o připojení služby Azure Cosmos DB jako je například koncový bod účtu a hlavní klíč do webové aplikace jako nastavení aplikace nebo vlastního připojovacího řetězce. Například možná máte vlastní webovou aplikaci, která byste chtěli nasadit pomocí účtu služby Azure Cosmos DB a mít informace o připojení, které jsou automaticky naplněny během nasazení.

> [!TIP]
> Šablonu nelze ověřit, že jsou název webové aplikace a název účtu služby Azure Cosmos DB zadali dole (a) platná a (b) k dispozici.  Důrazně doporučujeme, abyste ověřili dostupnost názvy, které chcete zadat před odesláním nasazení.
> 
> 

1. Na webu [Azure Portal](https://portal.azure.com)klikněte na nový a vyhledejte "Template Deployment".
    ![snímku uživatelského rozhraní nasazení šablony](./media/create-website/TemplateDeployment1.png)
2. Vyberte Template deployment položku a klikněte na tlačítko **vytvořit** ![obrazovky uživatelského rozhraní nasazení šablony](./media/create-website/TemplateDeployment2.png)
3. Klikněte na **Upravit šablonu**, vložte obsah souboru šablony DocDBWebSite. JSON a klikněte na **Uložit**.
   ![snímku uživatelského rozhraní nasazení šablony](./media/create-website/TemplateDeployment3.png)
4. Klikněte na **Upravit parametry**, zadejte hodnoty pro každý z povinných parametrů a klikněte na **OK**.  Parametry jsou následující:
   
   1. Název webu: Určuje název webové aplikace služby App Service a slouží k vytvoření adresu URL, kterou budete používat pro přístup k webové aplikaci (například když zadáte "mydemodocdbwebapp" a pak adresu URL, pomocí kterého přistupujete webové aplikace je mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: Určuje název plánu hostování služby App Service vytvořit.
   3. UMÍSTĚNÍ: Určuje umístění Azure, ve kterém chcete vytvořit službu Azure Cosmos DB a webových prostředků aplikace.
   4. DATABASEACCOUNTNAME: Určuje název účtu služby Azure Cosmos DB k vytvoření.   
      
      ![Snímek obrazovky uživatelské rozhraní pro nasazení šablony](./media/create-website/TemplateDeployment4.png)
5. Zvolte existující skupinu prostředků nebo zadejte název, který chcete vytvořit novou skupinu prostředků a vyberte umístění pro skupinu prostředků.

    ![Snímek obrazovky uživatelské rozhraní pro nasazení šablony](./media/create-website/TemplateDeployment5.png)
6. Kliknutím na **zkontrolovat právní předpisy**, **koupit**a pak kliknutím na **vytvořit** zahajte nasazení.  Vyberte **Připnout na řídicí panel** , aby se výsledné nasazení bylo možné snadno zobrazit na domovské stránce Azure Portal.
   ![snímku uživatelského rozhraní nasazení šablony](./media/create-website/TemplateDeployment6.png)
7. Až se nasazení dokončí, otevře se podokno skupiny prostředků.
   ![snímku podokna skupiny prostředků](./media/create-website/TemplateDeployment7.png)  
8. V seznamu prostředky klikněte na prostředek webové aplikace a pak klikněte na **nastavení aplikace** ![snímek obrazovky skupiny prostředků](./media/create-website/TemplateDeployment9.png)  
9. Všimněte si, jak jsou nastavení aplikace, které jsou k dispozici pro koncový bod služby Azure Cosmos DB a každý z hlavního klíče služby Azure Cosmos DB.

    ![Snímek obrazovky nastavení aplikace](./media/create-website/TemplateDeployment10.png)  
10. Nebojte se, že budete pokračovat v zkoumání webu Azure Portal, nebo pomocí jednoho z našich Azure Cosmos DB [ukázek](https://go.microsoft.com/fwlink/?LinkID=402386) vytvořit vlastní Azure Cosmos DBovou aplikaci.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Další kroky
Blahopřejeme! Nasazení služby Azure Cosmos DB, webová aplikace App Service a ukázkovou webovou aplikaci pomocí šablon Azure Resource Manageru.

* Pokud se chcete dozvědět víc o Azure Cosmos DB, klikněte [sem](https://azure.microsoft.com/services/cosmos-db/).
* Další informace o Azure App Service Web Apps získáte kliknutím [sem](https://go.microsoft.com/fwlink/?LinkId=325362).
* Další informace o šablonách Azure Resource Manager získáte kliknutím [sem](https://msdn.microsoft.com/library/azure/dn790549.aspx).

## <a name="whats-changed"></a>Co se změnilo
* Průvodce změnou z webů na službu App Service naleznete v tématu: [Služba Azure App Service a její vliv na stávající služby Azure](https://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Pokud chcete začít používat službu Azure App Service před registrací k účtu Azure, přejděte k možnosti [Vyzkoušet službu App Service](https://go.microsoft.com/fwlink/?LinkId=523751), kde můžete okamžitě vytvořit krátkodobou úvodní webovou aplikaci. Není vyžadována platební karta a nevzniká žádný závazek.
> 
> 

