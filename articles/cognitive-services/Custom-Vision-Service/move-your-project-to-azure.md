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
ms.openlocfilehash: 6fac6531ea0a39796de13f95aee33b30dc91f131
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60816517"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>Postup přesunutí projektu omezenou zkušební verzí Azure

Custom Vision Service dokončení jeho přesun do Azure, podporu pro projekty omezenou zkušební verzí mimo systém Azure se ukončuje. Tento dokument se ukazují, jak použít vlastní rozhraní API pro zpracování obrazu ke zkopírování omezenou zkušební verzí projektu do prostředku Azure.

Podpora pro zobrazení omezenou zkušební verzí projektů na [webu Custom Vision](https://customvision.ai) skončila 25. března 2019. Tento dokument teď ukazuje, jak používat vlastní rozhraní API pro zpracování obrazu pomocí [skript pythonu migrace](https://github.com/Azure-Samples/custom-vision-move-project) na Githubu) duplicitního projektu k prostředku Azure.

Další podrobnosti, včetně klíčových termínů v omezené zkušební verze procesu, najdete [poznámky k verzi](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019) nebo na e-mailové komunikace pro vlastníky omezenou zkušební projekty.

[Skript migrace](https://github.com/Azure-Samples/custom-vision-move-project) umožňuje znovu vytvořit projekt stažením a odesláním všechny uvedené značky, oblastech a Image v aktuální iteraci. Bude vám nechat s novým projektem ve své nové předplatné, které lze poté trénování.

## <a name="prerequisites"></a>Požadavky

- Budete potřebovat platné předplatné Azure spojené s účtem Microsoft nebo chcete použít pro přihlášení k účtu Azure Active Directory (AAD) [webu Custom Vision](https://customvision.ai). 
    - Pokud nemáte účet Azure [vytvořit účet](https://azure.microsoft.com/free/) zdarma.
    - Úvod do Azure koncepty předplatná a prostředky, najdete [– Příručka pro vývojáře Azure.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions).
-  [Python](https://www.python.org/downloads/)
- [PIP](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Vytvoření vlastní vizi prostředků na webu Azure Portal

Použít službu Custom Vision Service pomocí Azure, budete muset vytvořit vlastní zpracování obrazu trénování a Predikcí prostředky v [webu Azure portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

Více projektů lze přidružit jeden prostředek. Další podrobnosti o [ceny a omezení](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) je k dispozici. A pokračujte v bezplatném používání služby Custom Vision Service, můžete vybrat úroveň F0 na webu Azure Portal. 

> [!NOTE]
> Při přesunu vlastní vize projektu do prostředku Azure, dědí základní [oprávnění]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) prostředku Azure. Pokud ostatní uživatelé ve vaší organizaci jsou vlastníky váš projekt je v prostředku Azure, bude mít přístup k projektu na [webu Custom Vision](https://customvision.ai). Obdobně odstranění vašich prostředků se odstraní vaše projekty.  

## <a name="find-your-limited-trial-project-information"></a>Najít informace o omezené zkušební verze projektu

Pokud chcete přesunout svůj projekt, musíte _projektu ID_ a _školení klíč_ pro projekt, který se pokoušíte migrovat. Pokud tyto informace nemáte, navštivte [ https://limitedtrial.customvision.ai/projects ](https://limitedtrial.customvision.ai/projects) získat ID a klíč pro každý z vašich projektů. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>Zkopírujte váš projekt do Azure pomocí vzorového kódu Pythonu

Postupujte podle [ukázkový kód pokyny](https://github.com/Azure-Samples/custom-vision-move-project)pomocí omezenou zkušební klíč a ID projektu jako "zdroj" materiály a klíče z nového prostředku Azure, kterou jste vytvořili jako "cíl".

Standardně jsou všechny projekty omezenou zkušební verzí hostované v Jižní střední USA oblast Azure.

## <a name="next-steps"></a>Další postup

Váš projekt nyní byl přesunut do prostředku Azure. Je potřeba aktualizovat trénování a Predikcí klíče ve všech aplikacích, které jste napsali.

Chcete-li zobrazit svůj projekt na [webu Custom Vision](https://customvision.ai), přihlaste se pomocí stejného účtu, který jste použili pro přihlášení na web Azure Portal. Pokud se váš projekt nezobrazí, zkontrolujte, že jste ve stejném adresáři v [webu Custom Vision](https://customvision.ai) jako adresář, kde jsou umístěny prostředky na webu Azure Portal. Na webu Azure portal i CustomVision.ai můžete vybrat adresáře z rozevírací nabídky uživatele v pravém horním rohu obrazovky.