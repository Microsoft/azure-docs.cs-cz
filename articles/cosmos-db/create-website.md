---
title: Nasazení webové aplikace se šablonou – Azure Cosmos DB
description: Zjistěte, jak nasadit účet Azure Cosmos DB, webové aplikace Služby Azure App Service a ukázkovou webovou aplikaci pomocí šablony Azure Resource Manager.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: sngun
ms.openlocfilehash: 2306dbe234e171ac613c33458df1990b767637df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128372"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Nasazení webových aplikací Azure Cosmos DB a Azure App Service pomocí šablony Azure Resource Manager
Tento kurz ukazuje, jak použít šablonu Azure Resource Manager k nasazení a integraci [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), webové aplikace Azure App [Service](https://go.microsoft.com/fwlink/?LinkId=529714) a ukázkové webové aplikace.

Pomocí šablon Azure Resource Manager můžete snadno automatizovat nasazení a konfiguraci prostředků Azure.  Tento kurz ukazuje, jak nasadit webovou aplikaci a automaticky nakonfigurovat informace o připojení účtu Azure Cosmos DB.

Po dokončení tohoto výukového programu, budete moci odpovědět na následující otázky:  

* Jak můžu použít šablonu Azure Resource Manageru k nasazení a integraci účtu Azure Cosmos DB a webové aplikace ve službě Azure App Service?
* Jak můžu použít šablonu Azure Resource Managerka k nasazení a integraci účtu Azure Cosmos DB, webové aplikace ve webových aplikacích služby App Service a aplikace pro webdeploy?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Požadavky
> [!TIP]
> Zatímco tento kurz nepředpokládá předchozí zkušenosti se šablonami Azure Resource Manager nebo JSON, pokud chcete upravit odkazované šablony nebo možnosti nasazení, pak je vyžadována znalost každé z těchto oblastí.
> 
> 

Než budete dodržovat pokyny v tomto kurzu, ujistěte se, že máte předplatné Azure. Azure je platforma založená na předplatném.  Další informace o získání předplatného naleznete v [tématech Možnosti nákupu](https://azure.microsoft.com/pricing/purchase-options/), [Členské nabídky](https://azure.microsoft.com/pricing/member-offers/)nebo [Bezplatná zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-download-the-template-files"></a><a id="CreateDB"></a>Krok 1: Stažení souborů šablon
Začněme stažením souborů šablon, které tento kurz vyžaduje.

1. Stáhněte [si účet Create a Azure Cosmos DB, Web Apps a nasaďte ukázkovou šablonu ukázkové ukázkové](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) ukázky ukázkové ukázky demo aplikace do místní složky (například C:\Azure Cosmos DBTemplates). Tato šablona nasazuje účet Azure Cosmos DB, webovou aplikaci služby App Service a webovou aplikaci.  Také automaticky konfiguruje webovou aplikaci pro připojení k účtu Azure Cosmos DB.
2. Stáhněte [si ukázkovou šablonu Create a Azure Cosmos DB](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) do místní složky (například C:\Azure Cosmos DBTemplates). Tato šablona nasazuje účet Azure Cosmos DB, webovou aplikaci služby App Service, a upravuje nastavení aplikace webu tak, aby bylo možné snadno zobrazit informace o připojení služby Azure Cosmos DB, ale nezahrnuje webovou aplikaci.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>Krok 2: Nasazení účtu Azure Cosmos DB, webové aplikace App Service a ukázky ukázky ukázky ukázky ukázky ukázky ukázky ukázky ukázky ukázky ukázky ukázky ukázky ukázky ukázky ukázky ukázky ukázky ukázky ukázky ukázky
Nyní nasadíme první šablonu.

> [!TIP]
> Šablona neověřuje, že název webové aplikace a název účtu Azure Cosmos DB zadané v následující šabloně jsou a) platné a b) dostupné.  Důrazně doporučujeme ověřit dostupnost názvů, které chcete zadat před odesláním nasazení.
> 
> 

1. Přihlaste se na [portál Azure Portal](https://portal.azure.com), klikněte na Nový a vyhledejte "Nasazení šablony".
    ![Snímek obrazovky uživatelského rozhraní pro nasazení šablony](./media/create-website/TemplateDeployment1.png)
2. Vyberte položku nasazení šablony a klikněte na **Vytvořit** ![snímek obrazovky uživatelského rozhraní pro nasazení šablony.](./media/create-website/TemplateDeployment2.png)
3. Klepněte na **tlačítko Upravit šablonu**, vložte obsah souboru šablony DocDBWebsiteTodo.json a klepněte na **tlačítko Uložit**.
   ![Snímek obrazovky uživatelského rozhraní pro nasazení šablony](./media/create-website/TemplateDeployment3.png)
4. Klepněte na **tlačítko Upravit parametry**, zadejte hodnoty pro každý povinný parametr a klepněte na tlačítko **OK**.  Funkce má následující parametry:
   
   1. SITENAME: Určuje název webové aplikace Služby aplikace a slouží k vytvoření adresy URL, kterou používáte pro přístup k webové aplikaci (například pokud zadáte `mydemodocdbwebapp.azurewebsites.net`"mydemodocdbwebapp", pak je adresa URL, podle které přistupujete k webové aplikaci ).
   2. HOSTINGPLANNAME: Určuje název hostování služby App Service plán vytvořit.
   3. UMÍSTĚNÍ: Určuje umístění Azure, ve kterém chcete vytvořit Azure Cosmos DB a prostředky webové aplikace.
   4. DATABASEACCOUNTNAME: Určuje název účtu Azure Cosmos DB k vytvoření.   
      
      ![Snímek obrazovky uživatelského rozhraní pro nasazení šablony](./media/create-website/TemplateDeployment4.png)
5. Zvolte existující skupinu prostředků nebo zadejte název pro vytvoření nové skupiny prostředků a zvolte umístění pro skupinu prostředků.

    ![Snímek obrazovky uživatelského rozhraní pro nasazení šablony](./media/create-website/TemplateDeployment5.png)
6. Klikněte na **Zkontrolovat právní podmínky**, **Nákup**a potom na **Vytvořit** a začněte s nasazením.  Vyberte **Připnout na řídicí panel, aby** se výsledné nasazení snadno zviditelnilo na domovské stránce portálu Azure.
   ![Snímek obrazovky uživatelského rozhraní pro nasazení šablony](./media/create-website/TemplateDeployment6.png)
7. Po dokončení nasazení se otevře podokno skupiny prostředků.
   ![Snímek obrazovky podokna skupiny prostředků](./media/create-website/TemplateDeployment7.png)  
8. Chcete-li aplikaci používat, přejděte na adresu URL webové `http://mydemodocdbwebapp.azurewebsites.net`aplikace (ve výše uvedeném příkladu by adresa URL byla ).  Zobrazí se následující webová aplikace:
   
   ![Ukázková aplikace Todo](./media/create-website/image2.png)
9. Pokračujte a vytvořte několik úkolů ve webové aplikaci a pak se vraťte do podokna skupiny prostředků na webu Azure Portal. Klikněte na prostředek účtu Azure Cosmos DB v seznamu Prostředky a potom klikněte na **Průzkumník dat**.
10. Spusťte výchozí dotaz "SELECT * FROM c" a zkontrolujte výsledky.  Všimněte si, že dotaz načetl reprezentaci JSON položek todo, které jste vytvořili v kroku 7 výše.  Nebojte se experimentovat s dotazy; Zkuste například spustit select * from c WHERE c.isComplete = true vrátit všechny položky todo, které byly označeny jako dokončené.
11. Můžete prozkoumat prostředí portálu Azure Cosmos DB nebo upravit ukázkovou aplikaci Todo.  Až budete připraveni, nasadíme další šablonu.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Krok 3: Nasazení účtu dokumentu a ukázky webové aplikace
Nyní nasadíme druhou šablonu.  Tato šablona je užitečná, abyste ukázali, jak můžete vložit informace o připojení Azure Cosmos DB, jako je koncový bod účtu a hlavní klíč do webové aplikace jako nastavení aplikace nebo jako vlastní připojovací řetězec. Například možná máte vlastní webovou aplikaci, kterou chcete nasadit s účtem Azure Cosmos DB a mít informace o připojení automaticky naplněny během nasazení.

> [!TIP]
> Šablona neověřuje, že název webové aplikace a název účtu Azure Cosmos DB zadané níže jsou a) platné a b) k dispozici.  Důrazně doporučujeme ověřit dostupnost názvů, které chcete zadat před odesláním nasazení.
> 
> 

1. Na [webu Azure Portal](https://portal.azure.com)klikněte na Nový a vyhledejte "Nasazení šablony".
    ![Snímek obrazovky uživatelského rozhraní pro nasazení šablony](./media/create-website/TemplateDeployment1.png)
2. Vyberte položku nasazení šablony a klikněte na **Vytvořit** ![snímek obrazovky uživatelského rozhraní pro nasazení šablony.](./media/create-website/TemplateDeployment2.png)
3. Klepněte na **tlačítko Upravit šablonu**, vložte obsah souboru šablony DocDBWebSite.json a klepněte na **tlačítko Uložit**.
   ![Snímek obrazovky uživatelského rozhraní pro nasazení šablony](./media/create-website/TemplateDeployment3.png)
4. Klepněte na **tlačítko Upravit parametry**, zadejte hodnoty pro každý povinný parametr a klepněte na tlačítko **OK**.  Funkce má následující parametry:
   
   1. SITENAME: Určuje název webové aplikace Služby aplikace a slouží k vytvoření adresy URL, kterou použijete pro přístup k webové aplikaci (například pokud zadáte "mydemodocdbwebapp", pak je adresa URL, kterou přistupujete k webové aplikaci, mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: Určuje název hostování služby App Service plán vytvořit.
   3. UMÍSTĚNÍ: Určuje umístění Azure, ve kterém chcete vytvořit Azure Cosmos DB a prostředky webové aplikace.
   4. DATABASEACCOUNTNAME: Určuje název účtu Azure Cosmos DB k vytvoření.   
      
      ![Snímek obrazovky uživatelského rozhraní pro nasazení šablony](./media/create-website/TemplateDeployment4.png)
5. Zvolte existující skupinu prostředků nebo zadejte název pro vytvoření nové skupiny prostředků a zvolte umístění pro skupinu prostředků.

    ![Snímek obrazovky uživatelského rozhraní pro nasazení šablony](./media/create-website/TemplateDeployment5.png)
6. Klikněte na **Zkontrolovat právní podmínky**, **Nákup**a potom na **Vytvořit** a začněte s nasazením.  Vyberte **Připnout na řídicí panel, aby** se výsledné nasazení snadno zviditelnilo na domovské stránce portálu Azure.
   ![Snímek obrazovky uživatelského rozhraní pro nasazení šablony](./media/create-website/TemplateDeployment6.png)
7. Po dokončení nasazení se otevře podokno skupiny prostředků.
   ![Snímek obrazovky podokna skupiny prostředků](./media/create-website/TemplateDeployment7.png)  
8. Klikněte na prostředek webové aplikace v seznamu Zdroje a potom klikněte na Snímek obrazovky **s nastavením** ![aplikace skupiny prostředků.](./media/create-website/TemplateDeployment9.png)  
9. Všimněte si, jak existují nastavení aplikace pro koncový bod Azure Cosmos DB a každý z hlavních klíčů Azure Cosmos DB.

    ![Snímek obrazovky s nastavením aplikace](./media/create-website/TemplateDeployment10.png)  
10. Můžete pokračovat v průzkumu portálu Azure Portal nebo sledovat jednu z našich [ukázek](https://go.microsoft.com/fwlink/?LinkID=402386) Azure Cosmos DB a vytvořit si vlastní aplikaci Azure Cosmos DB.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Další kroky
Blahopřejeme! Nasadíte Azure Cosmos DB, webovou aplikaci služby App Service a ukázkovou webovou aplikaci pomocí šablon Azure Resource Manager.

* Další informace o Azure Cosmos DB najdete [tady](https://azure.microsoft.com/services/cosmos-db/).
* Další informace o webových aplikacích Služby Azure App Service najdete [sem](https://go.microsoft.com/fwlink/?LinkId=325362).
* Další informace o šablonách Azure Resource Manageru najdete [tady](https://msdn.microsoft.com/library/azure/dn790549.aspx).

## <a name="whats-changed"></a>Co se změnilo
* Průvodce změnou z webů na app service najdete v [tématu: Azure App Service a její dopad na stávající služby Azure.](https://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Pokud chcete začít používat Azure App Service před registrací účtu Azure, přejděte k [možnosti vyzkoušet si App Service](https://go.microsoft.com/fwlink/?LinkId=523751), kde si můžete hned vytvořit krátkodobou úvodní webovou aplikaci. Nevyžaduje se žádná platební karta a nevzniká žádný závazek.
> 
> 

