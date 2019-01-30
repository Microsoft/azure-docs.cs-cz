---
title: Správa týmů a podtýmy v Content Moderator API – Content Moderator
titlesuffix: Azure Cognitive Services
description: Další informace o použití týmy a podtýmy v Content Moderator API pro služby Cognitive Services.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: a36da1787d5abd5f7dc1455823be55f1880c7ba5
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227127"
---
# <a name="manage-review-teams-and-subteams"></a>Správa týmů revize a podtýmy

Tým musí vytvořit před pomocí Content Moderatoru. Při přihlašování nahoru a název vašeho týmu, tento tým se stane výchozí tým. V nástroji pro kontrolu, můžete mít jenom jeden tým. Můžete však vytvořit více podtýmy. Podtýmy jsou užitečné při vytváření eskalace týmy nebo týmy vyhrazené kontrolujeme konkrétní kategorie obsahu. Můžete například odeslat obsah pro dospělé na jiný tým pro další kontrolu.

Toto téma vysvětluje, jak vytvořit podtýmy a rychle přiřadit kontroly v reálném čase. Můžete však použít [pracovních postupů](workflows.md) přiřadit revize, závislosti na konkrétních kritériích.

## <a name="go-to-the-teams-setting"></a>Přejděte na týmy nastavení

Chcete-li začít vytvářet dílčího týmu, vyberte **týmy** v nastavení nezobrazila.

![Nastavení týmu](images/0-teams-1.png)

## <a name="create-subteams"></a>Vytvoření podtýmy

Výchozí tým obsahuje všechny potenciální revidující; podtýmy jsou podmnožinou tohoto výchozí tým. Nelze přiřazovat někdo dílčího týmu Pokud nejsou již členové týmu výchozí, budete muset přidat všechny recenzenty do výchozí tým nyní. Klikněte na tlačítko pozvat na stránku týmu.

![Pozvat uživatele](images/invite-users.png)

### <a name="create-a-subteam"></a>Vytvoření dílčího týmu
Přejděte dolů do části dílčího týmu stránku týmu. Klikněte na tlačítko Přidat dílčího týmu. 

![Přidat dílčího týmu](images/1-teams-1.png)

### <a name="name-your-subteam"></a>Název dílčího vašeho týmu
Zadejte název vašeho dílčího týmu v dialogovém okně a potom klikněte na Uložit.

![Název dílčího týmu](images/1-Teams-2.PNG)

### <a name="assign-members-from-your-default-team"></a>Přiřazení členů z výchozí tým.
Klikněte na tlačítko Přidat člen přiřazení členů k jedné nebo více podtýmy z výchozí tým. K dílčího týmu můžete přidávat pouze existující uživatele. Přidat nové uživatele, kteří nejsou v nástroj pro recenze, vyzvat je pomocí tlačítka "Pozvánku" na stránce nastavení týmu.

![Přiřazení členů dílčího týmu](images/1-Teams-3.PNG)

## <a name="assign-reviews-to-your-subteams"></a>Přiřadit recenze vaší podtýmy

Jakmile vaše podtýmy vytvořit a přiřadit členy týmu, můžete začít přiřazovat image text kontrol a do těchto podtýmy. To se provádí z okna revize.
Pokud chcete přiřadit jednotlivých obrázků dílčího týmu, klikněte na tlačítko tří teček v pravém horním rohu obrázku, vyberte možnost přesunout do a vyberte dílčího týmu.

![Přiřadit subteam kontrole bitové kopie](images/3-review-image-subteam-1.png)

## <a name="switch-between-subteams-to-review-assigned-content"></a>Přepínání mezi podtýmy ke kontrole přiřazený obsah

Pokud jste členem jedné nebo více podtýmy, můžete přepínat mezi těmito podtýmy z řídicího panelu zkontrolujte nástroj. Pokud chcete zobrazit všechny aktuální probíhající kontroly, které patří dílčího týmu, vyberte zvolte dílčího týmu z karty bitové kopie.

![Přepínání mezi podtýmy](images/3-review-image-subteam-2.png)
