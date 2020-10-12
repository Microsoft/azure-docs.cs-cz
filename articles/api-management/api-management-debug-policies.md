---
title: Ladění zásad Azure API Management v Visual Studio Code | Microsoft Docs
description: Naučte se ladit zásady Azure API Management pomocí rozšíření Azure API Management Visual Studio Code.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/22/2020
ms.author: apimpm
ms.openlocfilehash: 4eb32243df219d721d7baae80984c45d0fc4cf25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91341875"
---
# <a name="debug-azure-api-management-policies-in-visual-studio-code"></a>Ladění zásad Azure API Management v Visual Studio Code

[Zásady](api-management-policies.md) v Azure API Management poskytují výkonné možnosti, které VYDAVATELům API pomůžou řešit různé aspekty, jako je ověřování, autorizace, omezování, ukládání do mezipaměti a transformace. Zásady představují kolekci příkazů, které se postupně provádí na základě požadavku nebo odezvy z rozhraní API. 

Tento článek popisuje, jak ladit zásady API Management pomocí [rozšíření Azure API Management pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 

> [!NOTE]
> Tato funkce je ve verzi Public Preview.

## <a name="prerequisites"></a>Požadavky

Pomocí tohoto [kurzu](get-started-create-service-instance.md) nejdřív vytvořte API Management instanci vývojové vrstvy.

Nainstalujte [Visual Studio Code](https://code.visualstudio.com/) a nejnovější verzi [rozšíření Azure API Management pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 

## <a name="restrictions-and-limitations"></a>Omezení a omezení

Tato funkce je k dispozici pouze ve vývojářské úrovni API Management. Každá instance API Management podporuje jenom jednu souběžnou relaci ladění.

## <a name="initiate-a-debugging-session"></a>Iniciování relace ladění

1. Spusťte Visual Studio Code
2. Přechod na rozšíření API Management v části rozšíření Azure
3. Najít instanci API Management pro ladění
4. Vyhledání rozhraní API a operace, která se má ladit
5. Klikněte pravým tlačítkem na operaci a vyberte možnost **Spustit ladění zásad** .

V tomto okamžiku se rozšíření pokusí zahájit a vytvořit relaci ladění s bránou API Management.

![zahájit ladění](media/api-management-debug-policies/initiate-debugging-session.png)

## <a name="send-a-test-request"></a>Odeslat žádost o test
Po navázání relace ladění otevře rozšíření nový editor, který nám umožní vytvořit a odeslat testovací požadavek HTTP k této operaci s využitím [rozšíření klienta REST](https://marketplace.visualstudio.com/items?itemName=humao.rest-client).

Všimněte si, že se do žádosti již přidala hlavička **OCP-APIM-Debug** . Tato hlavička je povinná a hodnota musí být nastavená na klíč předplatného na úrovni služby, který umožňuje aktivovat funkci ladění v bráně API Management.

Upravte požadavek HTTP v editoru podle vašeho testovacího scénáře. Potom kliknutím na **Odeslat žádost** odešlete žádost o test do brány API Management.

![Odeslat žádost o test](media/api-management-debug-policies/rest-client.png)

## <a name="debug-policies"></a>Ladění zásad
Po odeslání požadavku na test HTTP rozšíření otevře okno ladění, které zobrazuje efektivní zásady této operace, a zastaví první efektivní zásadu. 

![zásady ladění](media/api-management-debug-policies/main-window.png)

Pokud chcete postupovat podle kanálu zásad, můžete krokovat prostřednictvím jednotlivých zásad nebo nastavit zarážku na zásadu a krokovat přímo s touto zásadou. 

Na panelu **proměnné** můžete zkontrolovat hodnoty proměnných vytvořených systémem a uživatelem. Na panelu **zarážek** můžete zobrazit seznam všech zarážek, které byly nastaveny. Na panelu **zásobník volání** můžete zobrazit aktuální rozsah platných zásad. 

Pokud při provádění zásad dojde k chybě, zobrazí se podrobnosti o chybě v zásadě, kde k ní došlo. 

![výjimek](media/api-management-debug-policies/exception.png)

> [!TIP]
> Nezapomeňte ukončit relaci ladění kliknutím na tlačítko **zastavit** po dokončení.


## <a name="next-steps"></a>Další kroky

+ Přečtěte si další informace o [rozšíření API Management pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 
+ Nahlásit problémy v [úložišti GitHubu](https://github.com/Microsoft/vscode-apimanagement)

