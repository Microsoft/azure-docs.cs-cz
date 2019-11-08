---
title: Přesunutí omezeného zkušebního projektu do Azure
titleSuffix: Azure Cognitive Services
description: Máte v Custom Vision k dispozici omezený zkušební projekt? V tomto článku se dozvíte, jak ho přesunout do Azure pomocí skriptu migrace.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 0a2b48a6c046150f6e685ecda0c0d765342e0194
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818952"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>Postup přesunutí vašeho omezeného zkušebního projektu do Azure

Jak Custom Vision Service dokončí přesun do Azure, končí podpora omezených zkušebních projektů mimo Azure. V tomto dokumentu se dozvíte, jak používat rozhraní API pro Custom Vision ke zkopírování vašeho omezeného zkušebního projektu do prostředku Azure.

Podpora zobrazení omezených zkušebních projektů na [webu Custom Vision](https://customvision.ai) skončila 25. března 2019. V tomto dokumentu se teď dozvíte, jak používat rozhraní Custom Vision API s [migrací skriptu Pythonu](https://github.com/Azure-Samples/custom-vision-move-project) na GitHubu) a duplikovat svůj projekt do prostředku Azure.

Další podrobnosti, včetně klíčových termínů v procesu vyřazení omezených zkušebních verzí, najdete v [poznámkách k verzi](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019) nebo na e-mailovou komunikaci odeslané vlastníkům omezených zkušebních projektů.

[Skript pro migraci](https://github.com/Azure-Samples/custom-vision-move-project) umožňuje znovu vytvořit projekt stažením a odesláním všech značek, oblastí a obrázků v aktuální iteraci. V novém předplatném vám ponechá nový projekt, který pak můžete vyškolit.

## <a name="prerequisites"></a>Požadavky

- Budete potřebovat platné předplatné Azure přidružené k účtu účet Microsoft nebo Azure Active Directory (AAD), který chcete použít k přihlášení na [web Custom Vision](https://customvision.ai). 
    - Pokud nemáte účet Azure, [Vytvořte si účet](https://azure.microsoft.com/free/) zdarma.
    - Úvod do konceptů předplatných a prostředků Azure najdete v [příručce pro vývojáře Azure.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)
-  [Python](https://www.python.org/downloads/)
- [PIP](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Vytvoření prostředků Custom Vision v Azure Portal

Pokud chcete použít Custom Vision Service s Azure, budete muset vytvořit Custom Vision školení a předpovědi prostředků v [Azure Portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

K jednomu prostředku může být přidruženo více projektů. Další podrobnosti o [cenách a omezeních](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) jsou k dispozici. Pokud chcete nadále používat Custom Vision Service zdarma, můžete v Azure Portal vybrat úroveň F0. 

> [!NOTE]
> Když přesunete projekt Custom Vision do prostředku Azure, zdědí základní [oprávnění]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) tohoto prostředku Azure. Pokud jsou jiní uživatelé ve vaší organizaci vlastníci prostředku Azure, ve kterém je váš projekt, budou mít přístup k vašemu projektu na [Custom Vision webu](https://customvision.ai). Podobně při odstraňování vašich prostředků se odstraní vaše projekty.  

## <a name="find-your-limited-trial-project-information"></a>Hledání informací o aplikaci s omezeným počtem zkušebních verzí

Chcete-li přesunout projekt, budete potřebovat _ID projektu_ a _školicí klíč_ pro projekt, který se pokoušíte migrovat. Pokud tyto informace nemáte, navštivte [https://www.customvision.ai/projects](https://www.customvision.ai/projects) a Získejte ID a klíč pro každý z vašich projektů. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>Zkopírování projektu do Azure pomocí ukázkového kódu Pythonu

Postupujte podle [pokynů k ukázkovému kódu](https://github.com/Azure-Samples/custom-vision-move-project), pomocí vašeho omezeného zkušebního klíče a ID projektu jako "zdrojové" materiály a klíče z nového prostředku Azure, který jste vytvořili jako "cíl".

Ve výchozím nastavení se všechny omezené zkušební projekty hostují v Střed USA – jih oblasti Azure.

## <a name="next-steps"></a>Další kroky

Váš projekt byl nyní přesunut do prostředku Azure. V aplikacích, které jste napsali, budete muset aktualizovat klíče pro školení a předpovědi.

Pokud chcete svůj projekt zobrazit na [webu Custom Vision](https://customvision.ai), přihlaste se pomocí stejného účtu, který jste použili k přihlášení do Azure Portal. Pokud se váš projekt nezobrazuje, potvrďte prosím, že jste ve stejném adresáři [Custom Vision](https://customvision.ai) jako adresář, ve kterém se vaše prostředky nacházejí v Azure Portal. V Azure Portal i CustomVision.ai můžete svůj adresář vybrat v nabídce rozevíracího uživatele v pravém horním rohu obrazovky.