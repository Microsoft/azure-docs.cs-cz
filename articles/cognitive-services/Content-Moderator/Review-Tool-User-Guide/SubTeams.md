---
title: Týmy a subteams v obsahu moderátora API | Microsoft Docs
description: Další informace o použití týmy a subteams v obsahu moderátora API pro kognitivní služby.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/22/2017
ms.author: sajagtap
ms.openlocfilehash: 161c7cd8bac07d5ffc138297d98a40317a8d88fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342417"
---
# <a name="team-and-subteams"></a>Tým a Subteams #

Před použitím obsahu moderátora musí vytvoření týmu. Při přihlašování k souhrnným datům a název váš tým, tento tým bude váš tým výchozí. V nástroji Kontrola může mít pouze jeden tým. Můžete však vytvořit více subteams. Subteams jsou užitečné pro vytváření eskalace týmy nebo týmy vyhrazený pro kontrola specifických kategorií obsahu. Například můžete odeslat obsah pro dospělé na jiný tým pro další revize.

Toto téma vysvětluje, jak vytvořit subteams a rychle přiřadit recenze za chodu. Můžete však použít [pracovních](workflows.md) přiřadit recenze závislosti na konkrétních kritériích.

## <a name="go-to-the-teams-setting"></a>Přejděte na týmy nastavení ##

Chcete-li začít pracovat na vytváření dílčího týmu, vyberte **týmy** možnosti v části nastavení.

![Nastavení Team](images/0-teams-1.png)

## <a name="create-subteams"></a>Vytvoření subteams ##

Výchozí tým obsahuje všechny potenciální kontroloři; subteams jsou podmnožinou výchozí týmu. Nelze přiřadit někdo dílčího týmu Pokud tomu tak není již členové týmu výchozí, budete muset přidat všechny kontroloři týmu výchozí teď. Klikněte na tlačítko pozvání na stránku týmu.

![Pozvat uživatele](images/invite-users.png)

### <a name="1-create-a-subteam"></a>1. Vytvořte dílčího týmu.
Posuňte se dolů stránku týmu v sekci dílčího týmu. Klikněte na tlačítko Přidat dílčího týmu. 

![Přidat dílčího týmu](images/1-teams-1.png)

### <a name="2-name-your-subteam"></a>2. Název dílčího vašeho týmu.
V dialogovém okně zadejte název dílčího týmu a potom klikněte na tlačítko Uložit.

![Název dílčího týmu](images/1-Teams-2.PNG)

### <a name="3-assign-members-from-your-default-team"></a>3. Přiřadíte členy z výchozí tým.
Klikněte na tlačítko Přidat člena na jeden nebo více subteams přiřadit členy z výchozí tým. Stávající uživatele můžete přidat pouze k dílčího týmu. Pro přidání nových uživatelů, kteří nejsou v nástroji Kontrola, vyzvat je pomocí tlačítko "Pozvání" na stránce nastavení týmu.

![Přiřadit dílčího týmu členy](images/1-Teams-3.PNG)

## <a name="assign-reviews-to-your-subteams"></a>Přiřadit recenze vaše subteams ##

Jakmile budete mít vaše subteams vytvořit a přiřadit členové týmu, můžete spustit přiřazení bitové kopie a textu zkontroluje do těchto subteams. To se provádí z okna revize.
Pokud chcete přiřadit dílčího týmu jednotlivých bitovou kopii, klikněte na tlačítko se třemi tečkami v pravém horním rohu bitové kopie, vyberte Přesun do a vyberte dílčího týmu.

![Přiřadit zkontrolovat bitovou kopii k subteam](images/3-review-image-subteam-1.png)

## <a name="switch-between-subteams-to-review-assigned-content"></a>Přepínání mezi subteams ke kontrole přiřazené obsahu ##

Pokud jste členem jedné nebo více subteams, můžete přepínat mezi tyto subteams na řídicím panelu nástrojů Revize. Chcete-li zobrazit všechny aktuální čekající na vyřízení recenze patřící do dílčího týmu, vyberte zvolte dílčího týmu z karty bitové kopie.

![Přepínání mezi subteams](images/3-review-image-subteam-2.png)
