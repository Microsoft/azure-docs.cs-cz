---
title: Správa plánu App Service – Azure | Microsoft Docs
description: Naučte se provádět různé úlohy pro správu plánu App Service.
keywords: App Service, Azure App Service, škálování, plán služby App Service, změna, vytváření, Správa, Správa
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: a5e69209c30eae816837ce8f00a065231a5fd821
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067211"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Správa plánu App Service v Azure

[Plán Azure App Service](overview-hosting-plans.md) poskytuje prostředky, které aplikace App Service potřebuje ke spuštění. V této příručce se dozvíte, jak spravovat plán App Service.

## <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

> [!TIP]
> Pokud máte App Service Environment, přečtěte si téma [Vytvoření plánu App Service v App Service Environment](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Můžete vytvořit prázdný plán App Service, nebo můžete vytvořit plán jako součást vytváření aplikací.

1. V [Azure Portal](https://portal.azure.com)vyberte **Nový** > **web a mobilní zařízení**a pak vyberte **Webová aplikace** nebo jiný typ aplikace App Service.

2. Vyberte existující plán App Service nebo vytvořte plán pro novou aplikaci.

   ![Vytvořte aplikaci v Azure Portal.][createWebApp]

   Postup vytvoření plánu:

   a. Vyberte **[+] vytvořit nové**.

      ![Vytvořte plán App Service.][createASP] 

   b. V části **plán App Service**zadejte název plánu.

   c. Jako **umístění**vyberte vhodné umístění.

   d. Pro **cenovou úroveň**vyberte příslušnou cenovou úroveň pro službu. Výběrem **Zobrazit vše** zobrazíte další cenové možnosti, například **Free** a **Shared**. Po výběru cenové úrovně klikněte na tlačítko **Vybrat** .

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Přesunutí aplikace do jiného plánu App Service

Aplikaci můžete přesunout do jiného plánu App Service, pokud se zdrojový a cílový plán nacházejí ve _stejné skupině prostředků a zeměpisné oblasti_.

> [!NOTE]
> Azure nasadí každý nový App Service plán do jednotky nasazení interně označované jako webspace. Každá oblast může mít mnoho webspaces, ale aplikaci lze přesouvat pouze mezi plány, které jsou vytvořeny ve stejném webovém prostoru. App Service Environment je izolovaný webový prostor, takže aplikace se dají přesouvat mezi plány ve stejné App Service Environment, ale ne mezi plány v různých App Service prostředích.
>
> Při vytváření plánu nemůžete zadat webový prostor, ale je možné zajistit, aby byl plán vytvořen ve stejném webovém prostoru jako stávající plán. V krátkém případě jsou všechny plány vytvořené se stejnou kombinací skupiny prostředků a oblastí nasazeny do stejného webspace. Pokud jste například vytvořili plán ve skupině prostředků a a v oblasti B, pak bude libovolný plán, který následně vytvoříte ve skupině prostředků a a oblast B, nasazen do stejného webspace. Všimněte si, že plány nemůžou po vytvoření přesunout webspaces, takže nemůžete přesunout plán do stejného webspaceu jako jiný plán, a to tak, že ho přesunete do jiné skupiny prostředků.
> 

1. V [Azure Portal](https://portal.azure.com)přejděte do aplikace, kterou chcete přesunout.

1. V nabídce vyhledejte část **App Service plán** .

1. Vyberte **změnit App Service plán** pro otevření app Serviceho selektoru **plánu** .

   ![Selektor App Serviceho plánu][change] 

1. V selektoru **App Serviceho plánování** vyberte existující plán, do kterého chcete tuto aplikaci přesunout.   

Na stránce **Vybrat plán App Service** se zobrazují jenom plány, které jsou ve stejné skupině prostředků a zeměpisné oblasti jako plán App Service aktuální aplikace.

Každý plán má svou vlastní cenovou úroveň. Například přesunutí lokality z **bezplatné** úrovně na úroveň **Standard** umožňuje všem aplikacím, které jsou k ní přiřazeny, používat funkce a prostředky úrovně **Standard** . Přesun aplikace z vyšší vrstvy do plánu nižší úrovně znamená, že už nebudete mít přístup k určitým funkcím. Pokud vaše aplikace používá funkci, která není v cílovém plánu k dispozici, zobrazí se chyba s informacemi o tom, která funkce je používána, která není k dispozici. 

Například pokud jedna z vašich aplikací používá certifikáty SSL, může se zobrazit tato chybová zpráva:

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

V takovém případě, než budete moct aplikaci přesunout do cílového plánu, musíte mít jednu z těchto akcí:
- Škálujte cenovou úroveň cílového plánu na **Basic** nebo vyšší.
- Odeberte všechna připojení SSL k vaší aplikaci.

## <a name="move-an-app-to-a-different-region"></a>Přesunutí aplikace do jiné oblasti

Oblast, ve které vaše aplikace běží, je oblast plánu App Service. Nemůžete však změnit oblast plánu App Service. Pokud chcete aplikaci spustit v jiné oblasti, bude jedna alternativa klonování aplikací. Klonování vytvoří kopii vaší aplikace v novém nebo existujícím plánu App Service v jakékoli oblasti.

**Klonování aplikace** můžete najít v části **vývojové nástroje** v nabídce.

> [!IMPORTANT]
> Klonování má určitá omezení. Můžete si je přečíst v tématu [Azure App Service klonování aplikací](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Škálování plánu App Service

Postup horizontálního navýšení kapacity plánu App Service najdete v tématu věnovaném horizontálnímu navýšení [kapacity aplikace v Azure](manage-scale-up.md).

Pokud chcete škálovat počet instancí aplikace, přečtěte si téma [Ruční nebo automatické škálování počtu instancí](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Odstranit plán App Service

Aby nedošlo k neočekávaným poplatkům, při odstranění poslední aplikace v plánu App Service App Service také ve výchozím nastavení odstraní plán. Pokud se rozhodnete plán zachovat, změňte plán na úroveň **Free** , abyste se vám neúčtovali.

> [!IMPORTANT]
> App Service plánů, ke kterým nejsou přidružené žádné aplikace, se budou účtovat poplatky, protože nadále vyhradí nakonfigurované instance virtuálních počítačů.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Škálování aplikace v Azure](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
