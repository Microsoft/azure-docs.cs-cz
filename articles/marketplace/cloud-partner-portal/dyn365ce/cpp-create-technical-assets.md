---
title: Vytvořit Dynamics 365 Customer Engagement technických prostředků – Azure Marketplace | Dokumentace Microsoftu
description: Vytvoření technických prostředků pro Dynamics 365 Customer Engagement nabídky aplikace.
services: Dynamics 365 for Customer Engagement, Azure, Marketplace, Cloud Partner Portal, AppSource
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/29/2018
ms.author: pbutlerm
ms.openlocfilehash: 50c4fd512206cdf17ebb555acb88de2a3f74c2bd
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54082657"
---
# <a name="create-technical-assets-for-azure-application-offer"></a>Vytvoření technických prostředků pro nabídku aplikací Azure

Obvykle budete vyvíjet řešení s použitím [sady SDK pro Dynamics 365 Customer Engagement aplikací](https://docs.microsoft.com/dynamics365/customer-engagement/developer/get-started-sdk).  Řešení využít celou řadu forem, jak je popsáno v [modely programování pro Dynamics 365 Customer Engagement aplikací](https://docs.microsoft.com/dynamics365/customer-engagement/developer/programming-models).  Zvolit formulář, který nejlépe odpovídá vašim požadavkům řešení.  Při vývoji řešení, existuje několik problémů, které musí řešit, jako jsou možnosti rozšiřitelnosti, komponenty řešení a kompatibilita verzí.  Další informace najdete v tématu [seznámení s řešeními](https://docs.microsoft.com/dynamics365/customer-engagement/developer/introduction-solutions).

Většina řešení Dynamics 365 publikovaných na webu AppSource je spravovaných aplikací distribuovaných jako soubory balíčku.


## <a name="creating-and-storing-the-package"></a>Vytvoření a uložení balíčku

Paralelní dokumentaci vytváření Dynamics 365 pro Customer Engagement nabízí je najdete v části [publikování aplikace na AppSource](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource).  Následující obsažené témata podrobnosti o tom, jak vytvořit soubor balíčku řešení a nahrajte ho do úložiště Azure:

- [Krok 4: Vytvoření balíčku AppSource pro aplikaci](https://docs.microsoft.com/dynamics365/customer-engagement/developer/create-package-app-appsource) – vysvětluje, jak vytvořit soubor komprimované (zip), který představuje vaše spravovaná aplikace a obsahuje: prostředky složku řešení, vlastní kód knihovny DLL, soubor s informacemi o typ MIME, ikona balíčku AppSource, licence soubor podmínky (HTML) a obsah souborů (XML).
- [Krok 5: Váš balíček AppSource Store ve službě Azure Storage a generovat adresu URL s klíčem SAS](https://docs.microsoft.com/dynamics365/customer-engagement/developer/store-appsource-package-azure-storage) – vysvětluje, jak uložit soubor balíčku AppSource v účtu úložiště objektů Blob v Microsoft Azure a pomocí klíče sdíleného přístupového podpisu (SAS) můžete sdílet soubor balíčku. Váš soubor balíčku se načte z umístění služby Azure Storage k certifikaci a pro AppSource zkušebních verzí a publikování.


## <a name="next-steps"></a>Další postup

Pokud jste tak již neučinili, [vytvořit vaše Dynamics 365 Customer Engagement nabídky](./cpp-create-offer.md).  Pak budete připraveni [publikování vaší nabídky](./cpp-publish-offer.md).
