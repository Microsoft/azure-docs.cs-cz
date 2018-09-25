---
title: Správa verzí v aplikace LUIS
titleSuffix: Azure Cognitive Services
description: Verze umožňují sestavovat a publikovat různých modelů. Dobrým postupem je klonovat aktuální active vzor na jinou verzi aplikace před prováděním změn do modelu.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: db461191b70aabc322e570ecc814a076c21206f2
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033783"
---
# <a name="manage-versions"></a>Správa verzí

Verze umožňují sestavovat a publikovat různých modelů. Dobrým postupem je klonovat aktuální active vzor na jiný [verze](luis-concept-version.md) aplikace před prováděním změn do modelu. 

Pro práci s verzí, otevřete aplikaci tak, že vyberete jeho název na **Moje aplikace** stránce a pak vyberte **spravovat** v horním pruhu vyberte **verze** v levém navigačním panelu. 

Seznam verzí zobrazit, jaké verze jsou publikovány, kde jsou publikovány, a kterou verzi je aktuálně aktivní. 

[![](./media/luis-how-to-manage-versions/versions-import.png "Správa části, stránka verze")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Klonovat verze

1. Vyberte verzi, které chcete naklonovat vyberte **klonování** z panelu nástrojů. 

2. V **klonování verze** dialogové okno, zadejte název pro novou verzi, jako je například "0.2".

   ![Dialogové okno verze klonování](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > Verze ID může obsahovat pouze znaky, číslice nebo '.' a nesmí být delší než 10 znaků.
 
 Nová verze se zadaným názvem je vytvořen a nastavit jako aktivní verze.

## <a name="set-active-version"></a>Nastavit aktivní verzi

Ze seznamu vyberte verzi a potom vyberte **zkontrolujte aktivní** z panelu nástrojů. 

[![](./media/luis-how-to-manage-versions/versions-other.png "Správa části, stránka verze")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Import verze

1. Vyberte **Import verze** z panelu nástrojů. 

2. V **Import nové verze** automaticky otevírané okno, zadejte název nové verze deset znaků. Stačí nastavit ID verze, pokud už existuje verze v souboru JSON v aplikaci.

    ![Správa části, stránka verze](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Jakmile dokončíte import verze, nová verze stane aktivní verze.

<a name = "export-version"></a>

## <a name="other-actions"></a>Další akce

* K **odstranit** verze, ze seznamu vyberte verzi a potom vyberte **odstranit** z panelu nástrojů. Vyberte **OK**. 
* K **přejmenovat** verze, ze seznamu vyberte verzi a potom vyberte **přejmenovat** z panelu nástrojů. Zadejte nový název a vyberte **provádí**. 
* K **exportovat** verze, ze seznamu vyberte verzi a potom vyberte **Export aplikace** z panelu nástrojů. Soubor se stáhne do místního počítače. 

