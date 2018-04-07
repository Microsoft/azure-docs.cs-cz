---
title: Nasazení webové aplikace pomocí šablony - Azure Cosmos DB | Microsoft Docs
description: Zjistěte, jak nasadit Azure Cosmos DB účet Azure App Service Web Apps a ukázkové webové aplikaci pomocí šablony Azure Resource Manager.
services: cosmos-db, app-service\web
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: 087d8786-1155-42c7-924b-0eaba5a8b3e0
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 468edd78e0ef8e13b2783485004a9db21906f1fd
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Nasazení databáze Cosmos Azure a Azure App Service Web Apps pomocí šablony Azure Resource Manager
V tomto kurzu se dozvíte, jak používat šablonu Azure Resource Manager k nasazení a integraci [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) webové aplikace a ukázkové webové aplikaci.

Pomocí šablony Azure Resource Manager, můžete snadno automatizovat nasazení a konfigurace prostředků Azure.  Tento kurz ukazuje, jak nasadit webovou aplikaci a automaticky konfigurovat informace o připojení účtu Azure Cosmos DB.

Po dokončení tohoto kurzu, budete moct odpovězte si na následující otázky:  

* Jak můžete použít šablonu Azure Resource Manager k nasazení a integraci účet Azure Cosmos DB a webové aplikace ve službě Azure App Service?
* Jak můžete použít šablonu Azure Resource Manager k nasazení a integrovat Azure Cosmos DB účet a webové aplikace v App Service Web Apps a aplikaci Webdeploy?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Požadavky
> [!TIP]
> Při tomto kurzu nepředpokládá předchozí zkušenosti s šablon Azure Resource Manageru nebo JSON, by měla, který chcete upravit odkazované šablony nebo možnosti nasazení znalosti o každé z těchto oblastí je požadovaná.
> 
> 

Než budete postupovat podle pokynů v tomto kurzu, ujistěte se, že máte předplatné Azure. Azure je platforma, na základě předplatného.  Další informace o získání předplatného najdete v tématu [možnostech nákupu](https://azure.microsoft.com/pricing/purchase-options/), [nabízí člen](https://azure.microsoft.com/pricing/member-offers/), nebo [bezplatné zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a id="CreateDB"></a>Krok 1: Stáhněte soubory šablon
Začněme tím stahování souborů šablony, které tento kurz vyžaduje.

1. Stažení [vytvoření účtu Azure Cosmos DB webové aplikace a nasazení aplikace demo-ukázka](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) šablony do místní složky (například C:\Azure Cosmos DBTemplates). Tato šablona nasadí účet Azure Cosmos DB, webové aplikace služby App Service a webové aplikace.  Také automaticky nakonfiguruje webové aplikace pro připojení k účtu Azure Cosmos DB.
2. Stažení [vytvoření účtu Azure Cosmos DB a ukázkové webové aplikace](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) šablony do místní složky (například C:\Azure Cosmos DBTemplates). Tato šablona nasadí účtu Azure Cosmos DB, webové aplikace služby App Service a upraví nastavení aplikace v lokalitě, aby snadno prostor informace o připojení Azure Cosmos DB, ale nezahrnuje webové aplikace.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>Krok 2: Nasazení účtu Azure Cosmos DB, webové aplikace App Service a demo-Ukázka aplikace
Teď umožňuje nasadit první šablony.

> [!TIP]
> Šablonu nelze ověřit, zda jsou název webové aplikace a název účtu Azure Cosmos DB zadané v šabloně následující a) platné a b) k dispozici.  Důrazně doporučujeme, abyste ověřili dostupnost názvy, které chcete zadat před odesláním nasazení.
> 
> 

1. Přihlášení k [portálu Azure](https://portal.azure.com), klikněte na nový a vyhledejte řetězec "Nasazení šablony".
    ![Snímek obrazovky nasazení šablony uživatelského rozhraní](./media/create-website/TemplateDeployment1.png)
2. Vyberte položku šablony nasazení a klikněte na tlačítko **vytvořit** ![snímek obrazovky nasazení šablony uživatelského rozhraní](./media/create-website/TemplateDeployment2.png)
3. Klikněte na tlačítko **úpravy šablony**, vložte obsah souboru šablony DocDBWebsiteTodo.json a klikněte na tlačítko **Uložit**.
   ![Snímek obrazovky nasazení šablony uživatelského rozhraní](./media/create-website/TemplateDeployment3.png)
4. Klikněte na tlačítko **upravit parametry**, zadejte hodnoty pro každé z povinných parametrů a klikněte na tlačítko **OK**.  Parametry jsou následující:
   
   1. Název webu: Určuje název webové aplikace služby App Service a slouží k vytvoření adresu URL, kterou používáte pro přístup k webové aplikaci (například když zadáte "mydemodocdbwebapp" a pak adresu URL, pomocí kterého jste přístup k webové aplikace je mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: Určuje název hostování plán služby App Service k vytvoření.
   3. UMÍSTĚNÍ: Určuje umístění Azure, ve kterém chcete vytvořit databázi Cosmos Azure a webové prostředky aplikace.
   4. DATABASEACCOUNTNAME: Určuje název účtu Azure Cosmos DB k vytvoření.   
      
      ![Snímek obrazovky nasazení šablony uživatelského rozhraní](./media/create-website/TemplateDeployment4.png)
5. Vyberte existující skupinu prostředků nebo zadejte název, aby novou skupinu prostředků a vyberte umístění pro skupinu prostředků.

    ![Snímek obrazovky nasazení šablony uživatelského rozhraní](./media/create-website/TemplateDeployment5.png)
6. Klikněte na tlačítko **přečíst si právní podmínky**, **nákupu**a potom klikněte na **vytvořit** zahájíte nasazení.  Vyberte **připnout na řídicí panel** výsledné nasazení je snadno zobrazovat na portálu Azure domovské stránky.
   ![Snímek obrazovky nasazení šablony uživatelského rozhraní](./media/create-website/TemplateDeployment6.png)
7. Až se nasazení dokončí, otevře se v podokně skupiny prostředků.
   ![Snímek obrazovky podokna skupiny prostředků](./media/create-website/TemplateDeployment7.png)  
8. K používání aplikace, přejděte na adresu URL webové aplikace (v příkladu nahoře, adresa URL bude http://mydemodocdbwebapp.azurewebsites.net).  Se zobrazí následující webové aplikace:
   
   ![Ukázková aplikace Todo](./media/create-website/image2.png)
9. Pokračujte a vytvořit několik úloh ve webové aplikaci a pak se vraťte do podokna skupinu prostředků na portálu Azure. Klikněte na prostředek Azure Cosmos DB účet do seznamu prostředků a potom klikněte na **Průzkumníku dat**.
10. Spusťte dotaz výchozí "vybrat * z c" a zkontrolovat výsledky.  Všimněte si, že dotaz je načíst reprezentaci JSON položky todo, které jste vytvořili v kroku 7 výše.  Nebojte se experimentovat s dotazy; Například spusťte vybrat * z c WHERE c.isComplete = true vrátit všechny položky todo, které byly označeny jako dokončené.
11. Nebojte se prozkoumat práce s portálem Azure Cosmos DB nebo upravit ukázkové aplikace Todo.  Až budete připraveni, nyní nasadíme jinou šablonu.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Krok 3: Nasaďte ukázkový dokumentu účet a webové aplikace
Teď umožňuje nasadit druhou šablonu.  Tuto šablonu lze použít ke jak je můžete vložit informace o připojení Azure Cosmos DB jako je koncový bod účtu a hlavní klíč do webové aplikace jako nastavení aplikace nebo jako vlastní připojovací řetězec. Například možná máte vlastní webové aplikace, které chcete nasadit pomocí účtu Azure Cosmos DB a mít informace o připojení vyplní automaticky během nasazení.

> [!TIP]
> Šablonu nelze ověřit, zda jsou název webové aplikace a název účtu Azure Cosmos DB zadali níže a) platné a b) k dispozici.  Důrazně doporučujeme, abyste ověřili dostupnost názvy, které chcete zadat před odesláním nasazení.
> 
> 

1. V [portálu Azure](https://portal.azure.com), klikněte na nový a vyhledejte řetězec "Nasazení šablony".
    ![Snímek obrazovky nasazení šablony uživatelského rozhraní](./media/create-website/TemplateDeployment1.png)
2. Vyberte položku šablony nasazení a klikněte na tlačítko **vytvořit** ![snímek obrazovky nasazení šablony uživatelského rozhraní](./media/create-website/TemplateDeployment2.png)
3. Klikněte na tlačítko **úpravy šablony**, vložte obsah souboru šablony DocDBWebSite.json a klikněte na tlačítko **Uložit**.
   ![Snímek obrazovky nasazení šablony uživatelského rozhraní](./media/create-website/TemplateDeployment3.png)
4. Klikněte na tlačítko **upravit parametry**, zadejte hodnoty pro každé z povinných parametrů a klikněte na tlačítko **OK**.  Parametry jsou následující:
   
   1. Název webu: Určuje název webové aplikace služby App Service a slouží k vytvoření adresu URL, kterou budete používat pro přístup k webové aplikaci (například když zadáte "mydemodocdbwebapp" a pak adresu URL, pomocí kterého jste přístup k webové aplikace je mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: Určuje název hostování plán služby App Service k vytvoření.
   3. UMÍSTĚNÍ: Určuje umístění Azure, ve kterém chcete vytvořit databázi Cosmos Azure a webové prostředky aplikace.
   4. DATABASEACCOUNTNAME: Určuje název účtu Azure Cosmos DB k vytvoření.   
      
      ![Snímek obrazovky nasazení šablony uživatelského rozhraní](./media/create-website/TemplateDeployment4.png)
5. Vyberte existující skupinu prostředků nebo zadejte název, aby novou skupinu prostředků a vyberte umístění pro skupinu prostředků.

    ![Snímek obrazovky nasazení šablony uživatelského rozhraní](./media/create-website/TemplateDeployment5.png)
6. Klikněte na tlačítko **přečíst si právní podmínky**, **nákupu**a potom klikněte na **vytvořit** zahájíte nasazení.  Vyberte **připnout na řídicí panel** výsledné nasazení je snadno zobrazovat na portálu Azure domovské stránky.
   ![Snímek obrazovky nasazení šablony uživatelského rozhraní](./media/create-website/TemplateDeployment6.png)
7. Až se nasazení dokončí, otevře se v podokně skupiny prostředků.
   ![Snímek obrazovky podokna skupiny prostředků](./media/create-website/TemplateDeployment7.png)  
8. Klikněte na prostředek webové aplikace do seznamu prostředků a potom klikněte na **nastavení aplikace** ![– snímek obrazovky skupiny prostředků](./media/create-website/TemplateDeployment9.png)  
9. Všimněte si, jak jsou k dispozici pro koncový bod Azure Cosmos DB a pro všechny hlavní klíče databáze Azure Cosmos nastavení aplikace.

    ![Snímek obrazovky nastavení aplikace](./media/create-website/TemplateDeployment10.png)  
10. Nebojte se dál zkoumat na portálu Azure nebo použijte jednu z našich Azure DB Cosmos [ukázky](http://go.microsoft.com/fwlink/?LinkID=402386) k vytvoření aplikace Azure Cosmos DB.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Další postup
Blahopřejeme! Jste nasadili Azure Cosmos databáze, webové aplikace App Service a ukázkové webové aplikaci pomocí šablony Azure Resource Manager.

* Další informace o databázi Cosmos Azure, klikněte na tlačítko [zde](http://azure.com/docdb).
* Další informace o Azure App Service Web apps, klikněte na tlačítko [zde](http://go.microsoft.com/fwlink/?LinkId=325362).
* Další informace o šablonách Azure Resource Manager, klikněte na tlačítko [zde](https://msdn.microsoft.com/library/azure/dn790549.aspx).

## <a name="whats-changed"></a>Co se změnilo
* Průvodce změnou z webů na službu App Service naleznete v tématu: [Služba Azure App Service a její vliv na stávající služby Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Pokud chcete začít používat službu Azure App Service před registrací k účtu Azure, přejděte k možnosti [Vyzkoušet službu App Service](http://go.microsoft.com/fwlink/?LinkId=523751), kde můžete okamžitě vytvořit krátkodobou úvodní webovou aplikaci. Není vyžadována platební karta a nevzniká žádný závazek.
> 
> 

