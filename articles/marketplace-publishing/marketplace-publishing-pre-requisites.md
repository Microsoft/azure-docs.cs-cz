---
title: Netechnická požadované součásti pro vytváření v rámci nabídky Azure Marketplace | Dokumentace Microsoftu
description: Pochopili požadavky na vytvoření a nasazení v rámci nabídky Azure Marketplace pro ostatní uživatele k nákupu.
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
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
ROBOTS: NOINDEX
ms.openlocfilehash: 4b925522186d2d9ae537431c1d96d39b107ad967
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073165"
---
# <a name="general-prerequisites-for-creating-an-offer-for-the-azure-marketplace"></a>Obecné požadavky pro vytváření v rámci nabídky Azure Marketplace
Seznamte se s Obecné, proces napjaté požadavky, které jsou potřeba k pokračovat v procesu vytváření nabídky.

## <a name="ensure-that-you-are-registered-as-a-seller-with-microsoft"></a>Ujistěte se, že jste zaregistrovaní jako prodejce s Microsoftem
Podrobné pokyny pro registraci účtem prodejce s Microsoftem, přejděte na [vytváření účtů a registraci](marketplace-publishing-accounts-creation-registration.md).

* Pokud chcete vytvořit novou nabídku společnosti je už zaregistrovaný jako prodejce v Centru pro vývojáře, pak se přihlaste k publikování se stejným ID e-mailu, s kterou Dev Center se provádí registraci portálu. Tento krok je nutný proto, že jsou mezi sebou propojeny portálu pro vývojáře a publikování.
* Pokud vaše společnost je už zaregistrovaný jako prodejce v Centru pro vývojáře a chcete upravit stávající nabídky, pak buď se přihlaste publikování portálu pomocí účtu správce nebo pomocí účtu, který je přidán jako coadmin v publikování portálu. Postup přidání účtu coadmin jsou uvedena níže.

## <a name="steps-to-add-a-coadmin-in-the-publishing-portal"></a>Postup přidání coadmin na portálu publikování
Správci portálu publikování můžete přidat ostatní členové podíváme na společnost, kteří pracují na aplikace, jako coadmin v publikování portálu. **Za předpokladu, že jste správce,** uvedena níže jsou kroky pro přidání coadmin.

> [!NOTE]
> Pro nové uživatele, před přidáním spolusprávce v publikování portál, ujistěte se, že jste vytvořili aspoň jednu aplikaci v publikování portálu. To je potřeba jako **VYDAVATELÉ** kartě se zobrazí pouze po vytvoření alespoň jednu aplikaci v publikování portálu.
> 
> 

1. Ujistěte se, že je ID e-mailu coadmin Microsoft account(MSA). Pokud ne, zaregistrovat jako použití této funkce MSA [odkaz](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Zajistěte, aby existovala alespoň jedna aplikace pod účtem správce, než se pokusíte přidat coadmin.
3. Po dokončení výše uvedené kroky přihlásit do k publikování portál coadmin e-mailem ID a potom Odhlásit se.
4. Teď se přihlaste k publikování portálu s ID správce e-mailu.
5. Přejděte na vydavatelé -> vyberte váš účet -> správci -> Přidat coadmin (snímek obrazovky níže uvedené)
   
    ![Kreslení](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)
6. Ujistěte se, že ID e-mailů, které jsou k dispozici v různých fázích procesu publikování (třeba Dev Center, portál pro publikování) jsou monitorovány žádné komunikaci od Microsoftu.
7. Pro vývojáře pro registraci nepoužívejte účet přidružený k jedné osobě. Tento návrh odebere závislost na jeden uživatel.
8. Pokud budete mít nějaké problémy s registrací Centrum vývojářů, vyvolat lístek pomocí tohoto [odkaz](https://developer.microsoft.com/en-us/windows/support).

## <a name="steps-to-delete-a-coadmin-in-the-publishing-portal"></a>Kroky pro odstranění coadmin v publikování portálu
**Za předpokladu, že jste správce,** uvedena níže jsou kroky pro odstranění coadmin.

1. Přihlaste se k publikování portálu s ID správce e-mailu.
2. Přejděte do **vydavatelé** -> vyberte váš účet -> **správci** -> **Spolusprávci**.
3. Klikněte na **X** tlačítko vedle coadmin chcete odstranit tot (níže uvedeném snímku obrazovky).
   
    ![Kreslení](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [!IMPORTANT]
> Není nutné pro dokončení společnosti daňové a bankovní informace, pokud máte v úmyslu publikovat pouze bezplatných nabídek (nebo používání vlastní licence).
> 
> Registrace společnosti musí dokončit, abyste mohli začít. Ale když vaše společnost pracuje na daňové a bankovní informace v účtu Microsoft Developer, vývojáři začít pracovat na vytvoření bitové kopie virtuálního počítače v [portálu pro publikování](https://publish.windowsazure.com), získat její certifikace a testování to v testovací prostředí Azure. Budete potřebovat účet schválení kompletní prodejce pouze za finální krok publikování vaší nabídky na webu Azure Marketplace.
> 
> 

## <a name="acquire-an-azure-pay-as-you-go-subscription"></a>Získat předplatné Azure "s průběžnými platbami"
Tato hodnota je předplatné, které použijete k vytvoření vašich imagí virtuálních počítačů a předá bitové kopie [Azure Marketplace](https://azure.microsoft.com/marketplace/). Pokud předplatné nemáte, potom se zaregistrujte na https://account.windowsazure.com/signup?offer=ms-azr-0003p.

## <a name="sell-from-countries"></a>"Zákazník z" země
> [!WARNING]
> Pokud chcete prodávat vaše služby na Azure Marketplace, ujistěte se, že je registrovaný entita z jedné ze zemí schválené "prodej z". Toto omezení je platební a daňové z důvodů. Těšíme se aktivně na tento seznam zemí, rozbalte v blízké budoucnosti, tak si Nenechte ujít. Úplný seznam najdete v části 1b [zásady účasti na webu Azure Marketplace](https://go.microsoft.com/fwlink/?LinkID=526833).
> 
> 

## <a name="next-steps"></a>Další postup
Po netechnické požadavky splněny, další jsou specifické pro nabídky technické požadavky. Klikněte na odkaz na článek pro typ příslušné nabídky, který chcete vytvořit pro Azure Marketplace.

* [Technické požadavky virtuálního počítače](marketplace-publishing-vm-image-creation-prerequisites.md)
* [Řešení šablony technické předpoklady](marketplace-publishing-solution-template-creation-prerequisites.md)

## <a name="see-also"></a>Další informace najdete v tématech
* [Začínáme: Publikování nabídky na webu Azure Marketplace](marketplace-publishing-getting-started.md)

