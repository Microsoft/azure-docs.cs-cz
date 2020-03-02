---
title: Jak nasadit nástroj pro označování ukázek pro rozpoznávání formulářů
titleSuffix: Azure Cognitive Services
description: Seznamte se s různými způsoby, jak můžete nasadit nástroj pro označování ukázek pro rozpoznávání formulářů, který vám umožní pomáhat s dohledem.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: pafarley
ms.openlocfilehash: fa419d7dd9668ac2ce8f2b0eb904117c7e22692d
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207841"
---
# <a name="deploy-the-sample-labeling-tool"></a>Nasazení ukázkového nástroje pro označování

Nástroj pro označování ukázkových popisků ve formuláři je aplikace, která běží v kontejneru Docker. Poskytuje užitečné uživatelské rozhraní, které můžete použít k ručnímu označení dokumentů formulářů k tomu, aby bylo možné pod dohledem učení. Průvodce [výukou s popisky](./quickstarts/label-tool.md) vám ukáže, jak spustit nástroj na místním počítači, což je nejběžnější scénář. 

Tato příručka vysvětluje alternativní způsoby, jak můžete nasadit a spustit vzorový Nástroj pro označování. 

## <a name="deploy-with-azure-container-instances"></a>Nasazení pomocí Azure Container Instances

Nástroj Label můžete spustit v kontejneru webové aplikace Docker. Nejprve [vytvořte nový prostředek webové aplikace](https://ms.portal.azure.com/#create/Microsoft.WebSite) na Azure Portal. Vyplňte formulář s podrobnostmi o předplatném a skupině prostředků. Do požadovaných polí zadejte následující informace:
* **Publikování**: kontejner Docker
* **Operační systém** Systém: Linux

Na další stránce vyplňte následující pole pro nastavení kontejneru Docker:

* **Možnosti**: jeden kontejner
* **Zdroj bitové kopie**: Azure Container Registry
* **Typ přístupu**: veřejné
* **Obrázek a značka**: MCR.Microsoft.com/Azure-Cognitive-Services/Custom-Form/labeltool:Latest

Postup, který následuje, je nepovinný. Po dokončení nasazení aplikace ji můžete spustit a získat přístup k nástroji Label online.

### <a name="connect-to-azure-ad-for-authorization"></a>Připojení k Azure AD pro autorizaci

Doporučujeme připojit webovou aplikaci ke službě Azure Active Directory (AAD), aby se aplikace mohla přihlásit a používat jenom osoba s přihlašovacími údaji. Pokud se chcete připojit k AAD, postupujte podle pokynů v části [Konfigurace aplikace App Service](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) .

## <a name="next-steps"></a>Další kroky

Vraťte se do [výukového programu s popisem](./quickstarts/label-tool.md) průvodce a Naučte se používat tento nástroj k ručnímu označení školicích dat a provádění vzdělávání pod dohledem.
