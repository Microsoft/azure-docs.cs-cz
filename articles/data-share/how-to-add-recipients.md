---
title: Přidání příjemců v Azure Data Share
description: Naučte se přidávat příjemce do existující sdílené složky v Azure Data Share.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: a8e3dac620873ab11ae24395310066037f6d2df4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680639"
---
# <a name="how-to-add-a-recipient-to-your-share"></a>Postup přidání příjemce do sdílené složky

Příjemce můžete přidat při vytváření nové sdílené složky nebo do existující sdílené složky. V uživatelském rozhraní sdílené složky Azure můžete přidat příjemce pomocí e-mailu pro přihlášení do Azure uživatele.  Z rozhraní API můžete použít kombinaci uživatelského objektu/instančního objektu a ID tenanta. Po zadání ID tenanta se dá Pozvánka přijmout jenom v tomto tenantovi. Z rozhraní API můžete také vytvořit pozvánku bez odeslání e-mailu příjemci. 

## <a name="add-recipient-to-an-existing-share"></a>Přidat příjemce do existující sdílené složky

V části Azure Data Share přejděte do odeslané sdílené složky a vyberte kartu **pozvánky** . Zde jsou uvedeni všichni příjemci pozvánky k této sdílené složce dat. Pokud chcete přidat nové, klikněte na **Přidat příjemce**.

![Snímek obrazovky se zobrazí vybraný přidat příjemce.](./media/how-to/how-to-add-recipients/add-recipient.png)

Na pravé straně stránky se zobrazí podokno. Klikněte na **Přidat příjemce** a potom v prázdném řádku vyplňte e-mail nového příjemce. Ujistěte se, že používáte přihlašovací e-mail Azure příjemce (pomocí e-mailového aliasu nefunguje). 

![Snímek obrazovky se zobrazí v podokně Přidat příjemce, kde můžete přidat a poslat pozvánku.](./media/how-to/how-to-add-recipients/add-recipient-side.png)

Klikněte na **Přidat a poslat pozvánku**. Noví příjemci budou posílat e-maily pozvánky této sdílené složce.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o tom, jak [Odstranit pozvánku ke sdílené složce](how-to-delete-invitation.md).
