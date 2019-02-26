---
title: Přesunout omezenou zkušební projekt do Azure
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak přesunout omezenou zkušební verzí projektu do Azure.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: a9f49af54f391b159f8b3d626fffc36635f5e51f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821298"
---
# <a name="how-to-move-your-limited-trial-project-to-azure-using-the-customvisionai-site"></a>Postup přesunutí omezenou zkušební verzí projektu do Azure pomocí webu CustomVision.ai


Custom Vision Service je nyní [Azure ve verzi Preview](https://azure.microsoft.com/services/preview/), podporu pro projekty omezenou zkušební verzí mimo systém Azure se ukončuje. V tomto dokumentu se dozvíte, jak používat [webu Custom Vision](https://customvision.ai) přesunout omezenou zkušební verzí projektu má být přidružena k prostředku Azure. 

> [!NOTE]
> Při přesunu Custom Vision projekty do prostředku Azure, jsou základní Zdědit [oprávnění]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) prostředku Azure. Pokud ostatní uživatelé ve vaší organizaci jsou vlastníky váš projekt je v prostředku Azure, bude mít přístup k projektu na [webu Custom Vision](https://customvision.ai). Obdobně odstranění vašich prostředků se odstraní vaše projekty.  


Úvod do Azure koncepty předplatná a prostředky, najdete [– Příručka pro vývojáře Azure.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)


## <a name="prerequisites"></a>Požadavky

Budete potřebovat platné předplatné Azure spojené s stejného účtu Microsoft nebo účet Azure Active Directory (AAD), který používáte pro přihlášení k [webu Custom Vision](https://customvision.ai). 

Pokud nemáte účet Azure [vytvořit účet](https://azure.microsoft.com/free/) zdarma.


## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Vytvoření vlastní vizi prostředků na webu Azure Portal
Použít službu Custom Vision Service pomocí Azure, budete muset vytvořit vlastní zpracování obrazu trénování a Predikcí prostředky v [webu Azure portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

 Přesuňte váš projekt pomocí tohoto [webu Custom Vision](https://customvision.ai) prostředí, je nutné vytvořit prostředky v oblasti střed USA – Jih, protože všechny projekty omezenou zkušební verzí, které jsou hostované v střed USA – jih. 

Více projektů lze přidružit jeden prostředek. Další podrobnosti o [ceny a omezení](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) je k dispozici. A pokračujte v bezplatném používání služby Custom Vision Service, můžete vybrat úroveň F0 na webu Azure Portal. 


## <a name="move-your-limited-trial-project-to-an-azure-resource"></a>Přesunout omezenou zkušební verzí projektu do prostředku Azure

1.  Ve webovém prohlížeči přejděte [webu Custom Vision](https://customvision.ai) a vyberte __přihlášení__. Otevřete projekt, který chcete migrovat na účet Azure. 
2.  Otevřete stránku nastavení pro váš projekt kliknutím na ikonu ozubeného kolečka v pravém horním rohu obrazovky. 

    ![Nastavení projektu je ikona ozubeného kolečka v pravém horním rohu stránky projektu.](./media/move-your-project-to-azure/settings-icon.png)


3. Klikněte na __do Azure přesunout__.

    ![Přesun do Azure tlačítko je vlevo dole na stránce nastavení projektu.](./media/move-your-project-to-azure/move-to-azure.jpg)


4. Z rozevíracího seznamu na __do Azure přesunout__ tlačítko, vyberte prostředek Azure, kterou chcete přesunout projekt tak, aby. Klikněte na tlačítko __přesunout__. 

5. Pokud nevidíte prostředků Azure, které jste vytvořili dříve pro Custom Vision Service, může být v jiném adresáři. Přesunout prostředek v jiném adresáři vašeho projektu, postupujte podle pokynů níže. 

    ![Okno projektu migrace.](./media/move-your-project-to-azure/Project_Migration_Window.jpg)


## <a name="move-project-to-another-azure-directory"></a>Projekt přesunout do jiného adresáře Azure 

> [!NOTE]
> Na webu Azure portal i CustomVision.ai můžete vybrat adresáře z rozevírací nabídky uživatele v pravém horním rohu obrazovky.   


1. Určete, který adresář je váš prostředek Azure v. Můžete najít adresář uvedený v části své uživatelské jméno v pravém horním řádku nabídek Azure portal. 

    ![Adresář je uvedena pod svoje uživatelské jméno v pravém horním řádku nabídek Azure portal. .](./media/move-your-project-to-azure/identify_directory.jpg)

2. Najdete ID prostředku vašeho prostředku Custom Vision školení. To můžete najít na webu Azure Portal otevřením Custom Vision školení prostředku a výběrem "Properties" v části "Správa prostředků". Vaše ID prostředku budou tam. 

    ![Vaše ID zdroje naleznete na webu Azure Portal otevřením Custom Vision školení prostředku a výběrem "Properties" v části "Správa prostředků".](./media/move-your-project-to-azure/resource_ID_azure_portal.jpg)


3. Alternativně můžete najít ID prostředku pro zpracování obrazu prostředku vlastní přímo na webu Custom Vision [stránka nastavení]( https://www.customvision.ai/projects#/settings). Je potřeba přepnout do stejného adresáře, se váš prostředek Azure.

    ![Vaše ID prostředku je uveden pro každý prostředek na stránce nastavení na webu Custom Vision.](./media/move-your-project-to-azure/resource_ID_CVS_portal.jpg)

4. Teď, když máte vaše ID prostředku, vraťte se do projektu vlastní zpracování obrazu, které se pokoušíte přejít z omezené zkušební verze na prostředek Azure. Připomenutí, budete muset přepněte zpátky na váš původní adresář ho najít. Postupujte podle pokynů uvedených [nad](#move-your-limited-trial-project-to-an-azure-resource) k otevření stránky nastavení vašeho projektu a vyberte __do Azure přesunout__. 


5. V přejít na okno Azure zaškrtněte políčko pro "Přesunout do jiného adresáře Azure?". Vyberte adresář, který chcete přesunout projekt tak, aby a zadejte ID prostředku prostředku, který se přesouvají do projektu. Klikněte na tlačítko __přesunout__. 



5. Nezapomeňte, že váš projekt je nyní v jiném adresáři. K vyhledání projektu, je potřeba přepnout do stejného adresáře na webovém portálu vlastní zpracování obrazu, který váš projekt je v. Jak na webu Azure Portal a [webu Custom Vision](https://customvision.ai), adresáře můžete vybrat z rozevíracího seznamu účet nabídky v pravém horním rohu obrazovky. 

## <a name="next-steps"></a>Další postup

Váš projekt nyní byl přesunut do prostředku Azure. Je potřeba aktualizovat trénování a Predikcí klíče ve všech aplikacích, které jste napsali.
