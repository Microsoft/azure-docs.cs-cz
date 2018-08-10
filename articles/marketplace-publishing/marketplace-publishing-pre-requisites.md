---
title: Netechnická požadované součásti pro vytváření v rámci nabídky Azure Marketplace | Dokumentace Microsoftu
description: Pochopili požadavky na vytvoření a nasazení v rámci nabídky Azure Marketplace pro ostatní uživatele k nákupu.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 3dae463b-8f48-4f52-8fa8-4e3975f09f43
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/18/2016
ms.author: hascipio
ms.openlocfilehash: 4f86d444a2f2b97fd8605d480db358813bc39fd3
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714185"
---
# <a name="general-prerequisites-for-creating-an-offer-for-the-azure-marketplace"></a>Obecné požadavky pro vytváření v rámci nabídky Azure Marketplace
Seznamte se s Obecné, proces napjaté požadavky, které jsou potřeba k pokračovat v procesu vytváření nabídky.

## <a name="ensure-that-you-are-registered-as-a-seller-with-microsoft"></a>Ujistěte se, že jste zaregistrovaní jako prodejce s Microsoftem
Podrobné pokyny pro registraci účtem prodejce s Microsoftem, přejděte na [vytváření účtů a registraci](marketplace-publishing-accounts-creation-registration.md).

* **Pokud vaše společnost je už zaregistrovaný jako prodejce v Centru pro vývojáře a chcete vytvořit novou nabídku** a přihlaste se k publikování se stejným id e-mailu, s kterou Dev Center se provádí registraci portálu. Tento krok je nutný proto, že jsou mezi sebou propojeny portálu pro vývojáře a publikování.
* **Pokud vaše společnost je už zaregistrovaný jako prodejce v Centru pro vývojáře a chcete upravit stávající nabídky,** pak buď Přihlaste se k publikování portálu pomocí účtu správce nebo pomocí účtu, který se přidá jako spolupracujícího Správce publikování portálu. Postup přidání spolusprávce účtu jsou uvedena níže.

## <a name="steps-to-add-a-co-admin-in-the-publishing-portal"></a>Postup přidání spolusprávce na portálu publikování
Správci portálu publikování můžete přidat ostatní členové podíváme na společnost, kteří pracují na aplikace, jako spolupracujícího Správce publikování portálu. **Za předpokladu, že jste správce,** níže uvedené kroky pro přidání spolusprávce.

> [!NOTE]
> Pro nové uživatele, před přidáním spolusprávce v publikování portál, ujistěte se, že jste vytvořili aspoň jednu aplikaci v publikování portálu. To je potřeba jako **VYDAVATELÉ** kartě se zobrazí pouze po vytvoření alespoň jednu aplikaci v publikování portálu.
> 
> 

1. Ujistěte se, že je id e-mailu spolusprávce Microsoft account(MSA). Pokud ne, zaregistrujte ho jako MSA použití této funkce [odkaz](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Ujistěte se, že existuje alespoň jedna aplikace pod účtem správce, než se pokusíte o přidání spolusprávce.
3. Po dokončení výše uvedené kroky, přihlaste se k publikování s id e-mailu spolupracujícího správce a přihlaste se na portálu.
4. Nyní Přihlaste se k publikování portálu s id e-mailu správce.
5. Přejděte na vydavatelé -> vyberte váš účet -> správci -> Přidat spolusprávce (snímek obrazovky níže uvedené)
   
    ![Kreslení](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)
6. Ujistěte se, že ID e-mailů, které jsou k dispozici v různých fázích procesu publikování (třeba Dev Center, portál pro publikování) jsou monitorovány žádné komunikaci od Microsoftu.
7. Pro vývojáře pro registraci nepoužívejte účet přidružený k jedné osobě. To je určeno pro odebrání závislostí jedna osoba.
8. Pokud budete mít nějaké problémy s registrací Centrum pro vývojáře, pak vyvolejte lístek pomocí tohoto [odkaz](https://developer.microsoft.com/en-us/windows/support).

## <a name="steps-to-delete-a-co-admin-in-the-publishing-portal"></a>Kroky pro odstranění spolupracujícího Správce publikování portálu
**Za předpokladu, že jste správce,** uvedena níže jsou kroky pro odstranění spolusprávce.

1. Přihlaste se k publikování portálu s id e-mailu správce.
2. Přejděte do **vydavatelé** -> vyberte váš účet -> **správci** -> **Spolusprávci**.
3. Klikněte na **X** tlačítko vedle spolusprávce chcete odstranit tot (níže uvedeném snímku obrazovky).
   
    ![Kreslení](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [!IMPORTANT]
> Není nutné pro dokončení společnosti daňové a bankovní informace, pokud máte v úmyslu publikovat pouze bezplatných nabídek (nebo používání vlastní licence).
> 
> Registrace společnosti musí dokončit, abyste mohli začít. Ale když vaše společnost pracuje na daňové a bankovní informace v účtu Microsoft Developer, vývojáři začít pracovat na vytvoření bitové kopie virtuálního počítače v [portálu pro publikování](https://publish.windowsazure.com), získat její certifikace a testování to v testovací prostředí Azure. Budete potřebovat účet schválení kompletní prodejce pouze za finální krok publikování vaší nabídky na webu Azure Marketplace.
> 
> 

## <a name="acquire-an-azure-pay-as-you-go-subscription"></a>Získat předplatné Azure "s průběžnými platbami"
Toto je předplatné, které použijete k vytvoření vašich imagí virtuálních počítačů a předá bitové kopie [Azure Marketplace](https://azure.microsoft.com/marketplace/). Pokud předplatné nemáte, pak zaregistrujte se prosím na https://account.windowsazure.com/signup?offer=ms-azr-0003p.

## <a name="sell-from-countries"></a>"Zákazník z" země
> [!WARNING]
> Pokud chcete prodávat vaše služby na Azure Marketplace, ujistěte se, že je registrovaný entita z jedné ze zemí schválené "prodej z". Toto omezení je platební a daňové z důvodů. Těšíme se aktivně na tento seznam zemí, rozbalte v blízké budoucnosti, tak si Nenechte ujít. Úplný seznam najdete v části 1b [zásady účasti na webu Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).
> 
> 

## <a name="next-steps"></a>Další postup
Jakmile netechnické požadavky splněny, se vedle nabídky konkrétní technické požadavky. Klikněte na odkaz na článek pro typ příslušné nabídky, který chcete vytvořit pro Azure Marketplace.

* [Technické požadavky virtuálního počítače](marketplace-publishing-vm-image-creation-prerequisites.md)
* [Řešení šablony technické předpoklady](marketplace-publishing-solution-template-creation-prerequisites.md)

## <a name="see-also"></a>Další informace najdete v tématech
* [Začínáme: publikování nabídky na webu Azure Marketplace](marketplace-publishing-getting-started.md)

