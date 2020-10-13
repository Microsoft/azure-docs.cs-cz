---
title: Řízení přístupu na základě role – Custom Vision
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak nakonfigurovat řízení přístupu na základě rolí pro projekty Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: 4eea87d7b5ef543551b789ddc6b045358fa98631
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826939"
---
# <a name="role-based-access-control"></a>Řízení přístupu na základě role

Custom Vision podporuje řízení přístupu na základě role Azure (Azure RBAC), což je autorizační systém pro správu individuálního přístupu k prostředkům Azure. Pomocí RBAC přiřadíte různým členům týmu různé úrovně oprávnění pro vaše projekty Custom Vision. Další informace o RBAC najdete v [dokumentaci k Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/).

## <a name="add-role-assignment-to-custom-vision-resource"></a>Přidání přiřazení role k Custom Vision prostředku

Službu Azure RBAC je možné přiřadit k prostředku Custom Vision. Pokud chcete udělit přístup k prostředku Azure, přidejte přiřazení role.
1. Na webu [Azure Portal](https://ms.portal.azure.com/) vyberte **Všechny služby**. 
1. Pak vyberte **Cognitive Services**a přejděte ke konkrétnímu prostředku Custom Vision školení.
   > [!NOTE]
   > Můžete také nastavit RBAC pro celé skupiny prostředků, odběry nebo skupiny pro správu. Provedete to tak, že vyberete požadovanou úroveň oboru a pak přejdete na požadovanou položku (například vyberete **skupiny prostředků** a potom kliknete na požadovanou skupinu prostředků).
1. V levém navigačním podokně vyberte **řízení přístupu (IAM)** .
1. Vyberte kartu **přiřazení rolí** a zobrazte přiřazení rolí pro tento obor.
1. Vyberte **Přidat**  ->  **přiřazení role přidat**.
1. V rozevíracím seznamu **role** vyberte roli, kterou chcete přidat.
1. V seznamu **Vybrat** vyberte uživatele, skupinu, instanční objekt nebo spravovanou identitu. Pokud v seznamu nevidíte objekt zabezpečení, můžete zadat pole vybrat a vyhledat v adresáři zobrazovaná jména, e-mailové adresy a identifikátory objektů.
1. Přiřaďte roli výběrem možnosti **Uložit**.

Během několika minut se cílové položce přiřadí vybraná role ve vybraném oboru.

## <a name="custom-vision-role-types"></a>Custom Vision typy rolí

K určení potřeb přístupu k prostředkům Custom Vision použijte následující tabulku.

|Role  |Oprávnění  |
|---------|---------|
|`Cognitive Services Custom Vision Contributor`     | Úplný přístup k projektům včetně možnosti vytvořit, upravit nebo odstranit projekt.        |
|`Cognitive Services Custom Vision Trainer`     | Úplný přístup s výjimkou možnosti vytvořit nebo odstranit projekt. Školitel může zobrazit a upravit projekty a vlaky, publikovat, zrušit publikování nebo exportovat modely.        |
|`Cognitive Services Custom Vision Labeler`     | Možnost nahrávání, úpravy nebo odstraňování školicích imagí a vytváření, přidávání, odebírání a odstraňování značek. Popisky můžou zobrazit projekty, ale nemůžou aktualizovat cokoli jiného než školicí image a značky.         |
|`Cognitive Services Custom Vision Deployment`     | Možnost publikovat, zrušit publikování nebo exportovat modely. Nasazení mohou zobrazit projekty, ale nemohou aktualizovat projekt, školicí snímky nebo značky.        |
|`Cognitive Services Custom Vision Reader`     | Možnost zobrazit projekty. Čtenáři nemůžou dělat žádné změny.        |
